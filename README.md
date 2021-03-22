# AWS-S3-Practice
# AWS-S3-Practice
AWS S3 Fileupload example in java 

## Amazon S3이란?
> Amazon Simple Storage Service는 인터넷 스토리지 서비스입니다. 이 서비스는 개발자가 더 쉽게 웹 규모 컴퓨팅 작업을 수행할 수 있도록 설계되었습니다.
> Amazon S3에서 제공하는 단순한 웹 서비스 인터페이스를 사용하여 언제든지 웹상 어디서나 원하는 양의 데이터를 저장하고 검색할 수 있습니다.
> 또한 개발자는 Amazon이 자체 웹 사이트의 글로벌 네트워크 운영에 사용하는 것과 같은 높은 확장성과 신뢰성을 갖춘 빠르고 경제적인 데이터 스토리지 인프라에 액세스할 수 있습니다.
> 이 서비스의 목적은 규모의 이점을 극대화하고 개발자들에게 이러한 이점을 제공하는 것입니다. <br><br> 
> 참고 : <a href="https://docs.aws.amazon.com/ko_kr/AmazonS3/latest/dev/Welcome.htm0l">Amazon S3이란 무엇인가요?</a>


## Amazon Simple Storage Service 
"Amazon Simple Storage Service", 인터넷 스토리지 서비스를 java 코드에서 수행 하도록 연습 파일

### 의존성 추가

~~~xml
      <!-- AWS -->
      <dependency>
         <groupId>com.amazonaws</groupId>
         <artifactId>aws-java-sdk-s3</artifactId>
         <version>1.11.901</version>
      </dependency>
~~~
<br>

### AwsS3 객체
#### - AwsS3.java

~~~java
public class AwsS3 {

   // Amazon-s3-sdk
   private AmazonS3 s3Client;
   final private String accessKey = "-"; // 액세스키
   final private String secretkey = "-"; // 스크릿 엑세스 키

   private Regions clientRegion = Regions.AP_NORTHEAST_2; // 한국
   private String bucket = "-"; // 버킷 명

   private AwsS3() {
      createS3Client();
   }

   // singleton 으로 구현
   static private AwsS3 instance = null;

   public static AwsS3 getInstance() {
      if (instance == null) {
         return new AwsS3();
      } else {
         return instance;
      }
   }

   // aws S3 client 생성
   private void createS3Client() {
      AWSCredentials credentials = new BasicAWSCredentials(accessKey, secretkey);
      this.s3Client = AmazonS3ClientBuilder.standard().withCredentials(new AWSStaticCredentialsProvider(credentials))
            .withRegion(clientRegion).build();
   }

   // upload 메서드 | 단일 파일 업로드
   public void upload(File file, String key) {
      uploadToS3(new PutObjectRequest(this.bucket, key, file));
   }

   // upload 메서드 | MultipartFile을 사용할 경우
   public void upload(File file, String key, String contentType, long contentLength) {
      ObjectMetadata objectMetadata = new ObjectMetadata();
      objectMetadata.setContentType(contentType);
   }

   // PutObjectRequest는 Aws s3 버킷에 업로드할 객체 메타 데이터와 파일 데이터로 이루어져 있다.
   private void uploadToS3(PutObjectRequest putObjectRequest) {
      try {
         this.s3Client.putObject(putObjectRequest);
         System.out.println(String.format("[%s] upload complete", putObjectRequest.getKey()));
      } catch (AmazonServiceException e) {
         e.printStackTrace();
      } catch (SdkClientException e) {
         e.printStackTrace();
      } catch (Exception e) {
         e.printStackTrace();
      }
   }


   // 복사 메서드
   public void copy(String orgkey, String copyKey) {
      try {
         // copy 객체 생성
         CopyObjectRequest copyObjectRequest = new CopyObjectRequest(this.bucket, orgkey, this.bucket, copyKey);

         // copy
         this.s3Client.copyObject(copyObjectRequest);

         System.out.printf(String.format("Finish copying [%s] to [%s]"), orgkey, copyKey);
      } catch (AmazonServiceException e) {
         e.printStackTrace();
      } catch (SdkClientException e) {
         e.printStackTrace();
      }
   }

   // 삭제 메서드
   public void delete(String key) {
      try {
         // Delete 객체 생성
         DeleteObjectRequest deleteObjectRequest = new DeleteObjectRequest(this.bucket, key);

         // Delete
         this.s3Client.deleteObject(deleteObjectRequest);

         System.out.printf(String.format("[%s] delete key"), key);
      } catch (AmazonServiceException e) {
         e.printStackTrace();
      } catch (SdkClientException e) {
         e.printStackTrace();
      }
   }
~~~

<br>

### 테스트 클래스
#### - main.java


~~~java
package kr.ac.zerco.aws.main;

import java.io.File;

import kr.ac.zerco.aws.AwsS3;

public class Main {
   
   public AwsS3 awsS3 = AwsS3.getInstance();
   
   public static void main(String[] args) {
      
      Main main = new Main();
      File file = new File("C:\\img\\log.png");
      
      String key = "img/mainlogo.png";
      //String copyKey = "img/my-img-copy.img";
      
      //upload 실행하기.
      main.upload(file,key);
   }
   
   //업로드
   public void upload(File file, String key) {
      awsS3.upload(file, key);
   }
   
   //복사 메서드
   public void copy(String orgkey, String copyKey) {
      awsS3.copy(orgkey, copyKey);
   }
   
   //삭제 메서드
   public void delete(String key) {
      awsS3.delete(key);
   }

}

~~~

<br>

### <실행결과>

<br>

![awss3](https://user-images.githubusercontent.com/55049159/111898929-6f19e080-8a6c-11eb-8d7e-bc87b4f8ad0e.PNG)


<br>

### 접근 URL : <a href="https://bkcbuc.s3.ap-northeast-2.amazonaws.com/images/logo/mainlogo.png">클릭</a>

<br>

### 참고

https://docs.aws.amazon.com/ko_kr/AmazonS3/latest/dev/UploadingObjects.html
