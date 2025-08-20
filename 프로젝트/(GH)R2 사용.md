---
date: 2025-07-02
tags:
  - R2
  - S3
  - AWS
---
## s3 sdk
아마존의 AWS S3 SDK를 사용해서 R2 서비스를 이용했다.
그래서 https://developers.cloudflare.com/r2/examples/aws/aws-sdk-java/ 
공식 문서에서 제시한 코드를 살펴보았다.
## 코드1 (공식)
```java
/**
 * Client for interacting with Cloudflare R2 Storage using AWS SDK S3 compatibility
 */
public class CloudflareR2Client {
    private final S3Client s3Client;

    /**
     * Creates a new CloudflareR2Client with the provided configuration
     */
    public CloudflareR2Client(S3Config config) {
        this.s3Client = buildS3Client(config);
    }

    /**
     * Configuration class for R2 credentials and endpoint
     */
    public static class S3Config {
        private final String accountId;
        private final String accessKey;
        private final String secretKey;
        private final String endpoint;

        public S3Config(String accountId, String accessKey, String secretKey) {
            this.accountId = accountId;
            this.accessKey = accessKey;
            this.secretKey = secretKey;
            this.endpoint = String.format(
            "https://%s.r2.cloudflarestorage.com", accountId);
        }

        public String getAccessKey() { return accessKey; }
        public String getSecretKey() { return secretKey; }
        public String getEndpoint() { return endpoint; }
    }

    /**
     * Builds and configures the S3 client with R2-specific settings
     */
    private static S3Client buildS3Client(S3Config config) {
        AwsBasicCredentials credentials = AwsBasicCredentials.create(
            config.getAccessKey(),
            config.getSecretKey()
        );

        S3Configuration serviceConfiguration = S3Configuration.builder()
            .pathStyleAccessEnabled(true)
            .build();

        return S3Client.builder()
            .endpointOverride(URI.create(config.getEndpoint()))
            .credentialsProvider(StaticCredentialsProvider.create(credentials))
            .region(Region.of("auto"))
            .serviceConfiguration(serviceConfiguration)
            .build();
    }

    /**
     * Lists all buckets in the R2 storage
     */
    public List<Bucket> listBuckets() {
        try {
            return s3Client.listBuckets().buckets();
        } catch (S3Exception e) {
            throw new RuntimeException(
            "Failed to list buckets: " + e.getMessage(), e);
        }
    }

    /**
     * Lists all objects in the specified bucket
     */
    public List<S3Object> listObjects(String bucketName) {
        try {
            ListObjectsV2Request request = ListObjectsV2Request.builder()
                .bucket(bucketName)
                .build();

            return s3Client.listObjectsV2(request).contents();
        } catch (S3Exception e) {
            throw new RuntimeException(
            "Failed to list objects in bucket "
            + bucketName + ": " + e.getMessage(), e);
        }
    }

    public static void main(String[] args) {
        S3Config config = new S3Config(
            "your_account_id",
            "your_access_key",
            "your_secret_key"
        );

        CloudflareR2Client r2Client = new CloudflareR2Client(config);

        // List buckets
        System.out.println("Available buckets:");
        r2Client.listBuckets().forEach(bucket ->
            System.out.println("* " + bucket.name())
        );

        // List objects in a specific bucket
        String bucketName = "demos";
        System.out.println("\nObjects in bucket '" + bucketName + "':");
        r2Client.listObjects(bucketName).forEach(object ->
            System.out.printf("* %s (size: %d bytes, modified: %s)%n",
                object.key(),
                object.size(),
                object.lastModified())
        );
    }
}
```

이건 이제 순수 Java를 위한 코드이고 이걸 스프링 프레임워크에 맞춰서 작성하면
## 코드2 (스프링)
```java
@Component
public class CloudflareR2Client {
    private final S3Client s3Client;

    public CloudflareR2Client(S3Client s3Client){
        this.s3Client = s3Client;
    }

    public List<Bucket> listBuckets() {
        try {
            return s3Client.listBuckets().buckets();
        } catch (S3Exception e) {
            throw new RuntimeException(
	            "Failed to list buckets: " + e.getMessage(), e);
        }
    }

    public List<S3Object> listObjects(String bucketName) {
        try {
            ListObjectsV2Request request = ListObjectsV2Request.builder()
                    .bucket(bucketName)
                    .build();

            return s3Client.listObjectsV2(request).contents();
        } catch (S3Exception e) {
            throw new RuntimeException(
	            "Failed to list objects in bucket " 
	            + bucketName + ": " + e.getMessage(), e);
        }
    }
}
```

```java
@Configuration
public class S3Config {
    @Value("${cloudflare.r2.account.id}")
    private String accountId;
    @Value("${cloudflare.r2.access.key}")
    private String accessKey;
    @Value("${cloudflare.r2.secret.key}")
    private String secretKey;

    @Bean
    public S3Client buildS3Client(){
        String endpoint = String.format(
        "https://%s.r2.cloudflarestorage.com", accountId);

        AwsBasicCredentials credentials = AwsBasicCredentials.create(
                accessKey, secretKey
        );

        S3Configuration serviceConfiguration = S3Configuration.builder()
                .pathStyleAccessEnabled(true)
                .build();

        return S3Client.builder()
                .endpointOverride(URI.create(endpoint))
                .credentialsProvider(
	                StaticCredentialsProvider.create(credentials))
                .region(Region.of("auto"))
                .serviceConfiguration(serviceConfiguration)
                .build();
    }
}
```

사실 `S3Client`를 그대로 사용하는건데 왜 이렇게 하는건지는 모르겠다.
파이널 프로젝트에서 썼던 s3config 코드랑 거의 흡사다하다.

```java
@Configuration
public class S3Config {
	
	@Value("${cloud.aws.credentials.access-key}")
	private String accessKey;
	
	@Value("${cloud.aws.credentials.secret-key}")
	private String secretKey;
	
	@Value("${cloud.aws.region.static}")
	private String region;
	
	@Bean
	public AmazonS3Client amazonS3Client() {
		// AWS 인증 정보 (Access Key, Secret Key) 설정
		BasicAWSCredentials credentials = new BasicAWSCredentials(accessKey, secretKey);
		
		return (AmazonS3Client) AmazonS3ClientBuilder
					.standard() // 기본설정
					.withRegion(region)
					.withCredentials(
					new AWSStaticCredentialsProvider(credentials))
					.build();
	}
}
```
## 문제점
래퍼 메소드를 계속 사용해야한다.
```java
 public void uploadImage(String bucket, String key, byte[] data){
        try{
            PutObjectRequest request = PutObjectRequest.builder()
                    .bucket(bucket)
                    .key(key)
                    .contentType("images")
                    .build();
            s3Client.putObject(request, RequestBody.fromBytes(data));
        } catch (S3Exception e){
            throw new RuntimeException("Failed to upload image"+e.getMessage());
        }
    }
```
이렇게 버켓과 연관되는 기능을 다 정의해야한다.
애초에 `s3client`만 사용한다면 service층에서 즉시 사용할 수 있겠지만.

# 에러
## image service
### 에러문
#### 1
ava.lang.RuntimeException: Failed to upload imageThe request signature we calculated does not match the signature you provided. Check your secret access key and signing method. (Service: S3, Status Code: 403, Request ID: null) (SDK Attempt Count: 1)
#### 2
```
=== Upload Debug Info === 
Bucket: temp 
Key: 2505241824089439733_1.png 
Data length: 663524 
=== Error Details === 
Status Code: 403 
Error Code: SignatureDoesNotMatch 
Error Message: The request signature we calculated does not match the signature you provided. Check your secret access key and signing method. 
Request ID: null
```
### 설명
#### 1
```java
public void uploadImage(String bucket, String key, byte[] data){
        System.out.println("=== Upload Debug Info ===");
        System.out.println("Bucket: " + bucket);
        System.out.println("Key: " + key);
        System.out.println("Data length: " + data.length);
        try{
            PutObjectRequest request = PutObjectRequest.builder()
                    .bucket(bucket)
                    .key(key)
                    .build();
            s3Client.putObject(request, RequestBody.fromBytes(data));
        } catch (S3Exception e){
            System.out.println("=== Error Details ===");
            System.out.println("Status Code: " + e.statusCode());
            System.out.println("Error Code: " + e.awsErrorDetails().errorCode());
            System.out.println(
	            "Error Message: " + e.awsErrorDetails().errorMessage()
            );
            System.out.println("Request ID: " + e.requestId());
            throw new RuntimeException("Failed to upload image"+e.getMessage());
        }
    }
```
여기서 S3Exception이 발생했던것.

그래서 api (account) 토큰도 두세번 다시 받아봤는데도 해결이 안됐다.
분명히 권한도 admin read & write로 줬는데.

`System.out.println(r2Client.listBuckets());`를 하면 버킷들 목록이 잘 등장했는데. 왜 업로드 할때만 안되지?

클로드가 시키는대로 PreSigned URL을 사용하는 방식으로 진행해봤다.
공식문서에서는 
#### 2
```java
public class CloudflareR2Client {
  private final S3Client s3Client;
  private final S3Presigner presigner;

    /**
     * Creates a new CloudflareR2Client with the provided configuration
     */
    public CloudflareR2Client(S3Config config) {
        this.s3Client = buildS3Client(config);
        this.presigner = buildS3Presigner(config);
    }

    /**
     * Builds and configures the S3 presigner with R2-specific settings
     */
    private static S3Presigner buildS3Presigner(S3Config config) {
        AwsBasicCredentials credentials = AwsBasicCredentials.create(
            config.getAccessKey(),
            config.getSecretKey()
        );

        return S3Presigner.builder()
            .endpointOverride(URI.create(config.getEndpoint()))
            .credentialsProvider(StaticCredentialsProvider.create(credentials))
            .region(Region.of("auto"))
            .serviceConfiguration(S3Configuration.builder()
                .pathStyleAccessEnabled(true)
                .build())
            .build();
    }

    public String generatePresignedUploadUrl(
	    String bucketName, 
	    String objectKey, 
	    Duration expiration) {
        PutObjectPresignRequest presignRequest =
	        PutObjectPresignRequest.builder()
	            .signatureDuration(expiration)
	            .putObjectRequest(builder -> builder
	                .bucket(bucketName)
	                .key(objectKey)
	                .build())
	            .build();

        PresignedPutObjectRequest presignedRequest =
	        presigner.presignPutObject(presignRequest);
        return presignedRequest.url().toString();
    }

    // Rest of the methods remains the same
    public static void main(String[] args) {
      // config the client as before

      // Generate a pre-signed upload URL valid for 15 minutes
        String uploadUrl = r2Client.generatePresignedUploadUrl(
            "demos",
            "README.md",
            Duration.ofMinutes(15)
        );
        System.out.println("Pre-signed Upload URL (valid for 15 minutes):");
        System.out.println(uploadUrl);
    }
}
```
그래서 나는
```java
@Configuration
public class S3Config {
    @Value("${cloudflare.r2.account.id}")
    private String accountId;
    @Value("${cloudflare.r2.access.key}")
    private String accessKey;
    @Value("${cloudflare.r2.secret.key}")
    private String secretKey;

    @Bean
    public S3Client buildS3Client(){
	    ...
    }

    @Bean
    public S3Presigner s3Presigner() {
        String endpoint = String.format("https://%s.r2.cloudflarestorage.com", accountId);

        return S3Presigner.builder()
                .endpointOverride(URI.create(endpoint))
                .credentialsProvider(StaticCredentialsProvider.create(
                        AwsBasicCredentials.create(accessKey, secretKey)))
                .region(Region.of("auto"))
                .build();
    }

}
```
이렇게 밑에 `S3Presigner`만 추가해줬다.
그리고 `r2Client` 클래스에 메소드를 추가했다.
```java
public String generateUploadUrl(String bucket, String key) {
        try {
            PutObjectRequest request = PutObjectRequest.builder()
                    .bucket(bucket)
                    .key(key)
                    .build();

            PutObjectPresignRequest presignRequest = 
	            PutObjectPresignRequest.builder()
                    .signatureDuration(Duration.ofMinutes(10))
                    .putObjectRequest(request)
                    .build();

            String url = 
	            s3Presigner.presignPutObject(presignRequest).url().toString();
            System.out.println("Generated PreSigned URL: " + url);
            return url;
        } catch (Exception e) {
            throw new RuntimeException(
	            "Failed to generate presigned URL: " + e.getMessage(), e);
        }
    }
```

그리고나서 
```java
String presignedUrl = r2Client.generateUploadUrl("temp", "presigned-test.txt");
System.out.println("Use this URL to upload: " + presignedUrl);
```
이걸 해봤더니 
```
Generated PreSigned URL: [https://temp.9af1ff692e57c2b144b127914c6d7c09.r2.cloudflarestorage.com/presigned-test.txt?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Date=20250524T095303Z&X-Amz-SignedHeaders=host&X-Amz-Credential=c4a6bd075832047b69d9c267867bcbe5%2F20250524%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Expires=600&X-Amz-Signature=2c3d37e7c2094eedb509f344763e314828b529e79737f00ad4e79a806f4a5f4d](https://temp.9af1ff692e57c2b144b127914c6d7c09.r2.cloudflarestorage.com/presigned-test.txt?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Date=20250524T095303Z&X-Amz-SignedHeaders=host&X-Amz-Credential=c4a6bd075832047b69d9c267867bcbe5%2F20250524%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Expires=600&X-Amz-Signature=2c3d37e7c2094eedb509f344763e314828b529e79737f00ad4e79a806f4a5f4d) Use this URL to upload: [https://temp.9af1ff692e57c2b144b127914c6d7c09.r2.cloudflarestorage.com/presigned-test.txt?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Date=20250524T095303Z&X-Amz-SignedHeaders=host&X-Amz-Credential=c4a6bd075832047b69d9c267867bcbe5%2F20250524%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Expires=600&X-Amz-Signature=2c3d37e7c2094eedb509f344763e314828b529e79737f00ad4e79a806f4a5f4d](https://temp.9af1ff692e57c2b144b127914c6d7c09.r2.cloudflarestorage.com/presigned-test.txt?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Date=20250524T095303Z&X-Amz-SignedHeaders=host&X-Amz-Credential=c4a6bd075832047b69d9c267867bcbe5%2F20250524%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Expires=600&X-Amz-Signature=2c3d37e7c2094eedb509f344763e314828b529e79737f00ad4e79a806f4a5f4d)
```
이렇게 긴 주소가 반환됐다.
그리고 버킷을 확인해보니 `presigned-test.txt`가 성공적으로 만들어졌다.

터미널을 켜서
`curl -X "<위의주소>" --data "Hello World Test"`를 해보니 비어있던 텍스트파일에 헬로월드 문구가 적혔다.
#### 3
그러니까 presigned url을 활용하니까 제대로 작동을 한건데 왤까?
클로드는 `S3Config`의 builder 메서드에 있는 `serviceConfig`에 `.chunkedEncodingEnabled(false)`를 추가해보라고 했다.
```java
    @Bean
    public S3Client buildS3Client(){
        String endpoint = String.format(
        "https://%s.r2.cloudflarestorage.com", accountId);

        AwsBasicCredentials credentials = AwsBasicCredentials.create(
                accessKey, secretKey
        );

        S3Configuration serviceConfig = S3Configuration.builder()
                .pathStyleAccessEnabled(true)
                .chunkedEncodingEnabled(false)  // 추가
                .build();

        return S3Client.builder()
                .endpointOverride(URI.create(endpoint))
                .credentialsProvider(
	                StaticCredentialsProvider.create(credentials))
                .region(Region.of("auto"))
                .serviceConfiguration(serviceConfig)
                .build();
    }
```
그랬더니 이제 사진이 올라간다.

### 정리
- `r2Client.listBuckets()`가 잘 작동한다 -> Credentials가 잘못 입력된건 아니라는것이다.
- PreSigned url을 생성할 수 있었고 `curl` 명령어로 여기에 업로드도 할 수 있었다. 

그런데 SDK를 통해서 `putObject()`만 계속 실패한다?

S3 호환 서비스들을 AWS SDK로 이용할때 흔히 발생하는 문제점들
- region 설정 차이
- path style vs virtual hosted style
- chunked transfer encoding 호환성
- HTTP client 설정 차이