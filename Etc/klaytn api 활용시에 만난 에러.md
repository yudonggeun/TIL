# klaytn api 를 활용한 개발시에 만난 에러 목록
1. 
```
***************************
APPLICATION FAILED TO START
***************************

Description:

An attempt was made to call a method that does not exist. The attempt was made from the following location:

    org.web3j.protocol.http.HttpService.performIO(HttpService.java:154)

The following method did not exist:

    'okhttp3.RequestBody okhttp3.RequestBody.create(java.lang.String, okhttp3.MediaType)'

The calling method's class, org.web3j.protocol.http.HttpService, was loaded from the following location:

    jar:file:/C:/Users/ydong/.gradle/caches/modules-2/files-2.1/org.web3j/core/4.6.0/fbd8dd54723c73d5b46c64f933e7ef85910b70f1/core-4.6.0.jar!/org/web3j/protocol/http/HttpService.class

The called method's class, okhttp3.RequestBody, is available from the following locations:

    jar:file:/C:/Users/ydong/.gradle/caches/modules-2/files-2.1/com.squareup.okhttp3/okhttp/3.14.9/3e6d101343c7ea687cd593e4990f73b25c878383/okhttp-3.14.9.jar!/okhttp3/RequestBody.class

The called method's class hierarchy was loaded from the following locations:

    okhttp3.RequestBody: file:/C:/Users/ydong/.gradle/caches/modules-2/files-2.1/com.squareup.okhttp3/okhttp/3.14.9/3e6d101343c7ea687cd593e4990f73b25c878383/okhttp-3.14.9.jar


Action:

Correct the classpath of your application so that it contains compatible versions of the classes org.web3j.protocol.http.HttpService and okhttp3.RequestBody
```

현상 : package org.web3j.protocol.http.HttpService에서 performIO 메서드에서 RequestBody.create(request, JSON_MEDIA_TYPE); 문장에서 에러가 발생한 것이다.

원인 : web3j 라이브러리 버전을 업그레이드하면 해결된다. 구버전과 호환되지 않은 부분으로 인해서 발생한 것으로 보임

해결 : 
```
implementation 'org.web3j:core:5.0.0'
implementation 'com.squareup.okhttp3:okhttp:4.9.3'
```
다음 코드를 build.gradle에 추가하면 web3j 버전을 맞출 수 있다.

도움 받은 사이트 : https://github.com/web3j/web3j/issues/1271

2.
```
* What went wrong:
Execution failed for task ':core:compileJava'.
> java.lang.IllegalAccessError: class org.gradle.internal.compiler.java.ClassNameCollect
or (in unnamed module @0x127eb24e) cannot access class com.sun.tools.javac.code.Symbol$T
ypeSymbol (in module jdk.compiler) because module jdk.compiler does not export com.sun.t
ools.javac.code to unnamed module @0x127eb24e
```

현상 : core:compileJava 작업 빌드가 계속해서 실패

원인 : gradle wapper 가 명시한 버전과 로컬의 자바 sdk 버전이 일치하지 않아서 발생

삽질 
* a. intellij 세팅 변경 : ide에서 프로젝트 sdk 버전을 변경해서 빌드를 계속해서 시도했다. 여러가지 버전으로 다 바꾸어서 시도를 했으나 여전히 같은 현상이 발견이 되어서 실패

* b. file encoding 변경 : 실패한 원인은 아니지만 이것 저것 해결을 해보겠다고 파일인코딩이 잘못된 warm 메시지를 수정하면 될까? 하고 헛된 희망을 가지고 실행했으나 결국은 원인이 인코딩 문제가 아니기 때문에 실패

해결
gradle에 대한 자료 조사 및 공부를 하는 것이 에러를 찾기에 빠르다고 판단하여 gardle에 대한 조사를 실행했을 때 나와 비슷한 현상을 나타내는 글을 발견함. 해당 글에서 ide의 설정을 변경해도 gradlew 로 빌드했을 때 차이점이 없다는 것을 공감했다. 해결 방법은 gradlew에서 강제로 sdk path를 옵션으로 주어서 실행하는 것이었다. 실행 결과 성공~~~!! (프로젝트에 targetCompatibility=1.8과 공식 문서에서도 sdk 1.8 이상에서 지원한다는 점에서 sdk 1.8을 지원한다는 것을 유추할 수 있고 실제로 성공함)

gradlew.bat :console:shadowDistZip -Dorg.gradle.java.home="sdk path"

도움 받은 사이트 : https://chinsun9.github.io/2021/04/22/gradle-build-%ED%8A%B9%EC%A0%95-jdk-%EB%B2%84%EC%A0%84%EC%9C%BC%EB%A1%9C/