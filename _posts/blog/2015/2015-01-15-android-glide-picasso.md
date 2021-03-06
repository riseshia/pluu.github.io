---
layout: post
title: "Android Picasso vs Glide"
date: 2015-01-15 11:23:00
tag: [Android, Android-Library, Picasso, Glide]
categories:
- blog
- Android
---

Android에서 사용하는 ImageLoader Library중에서 편리중심이며 많이 사용하고 있는 Picasso와

Google I/O 2014로 인해서 급부상항 Glide에 대해서 가벼운 정보를 작성했습니다.

<!--more-->

## Picasso
Square Inc 개발 Library

- GitHub : http://square.github.io/picasso/
- Wiki : http://square.github.io/picasso/

최신버전 2.4.0

## Glide
Google I/O 2014 소스에 포함된후 급부상

왠지모를 Picasso의 Copy & Paste ??

최신버전 3.4.0

- GitHub : https://github.com/bumptech/glide
- Wiki : https://github.com/bumptech/glide/wiki

- - -

## 기본 환경 구성 (Gradle Base)


```groovy
// Picasso
dependencies {
   compile 'com.squareup.picasso:picasso:2.4.0'
}

// Glide
dependencies {
   compile 'com.github.bumptech.glide:glide:3.4.+'
}
```

- - -

## 사용방법 비교

### 기본

```java
// Picasso
Picasso.with(this)
   .load(URL)
   .into(ImageView imageView);

// Glide
Glide.with(this)
   .load(URL)
   .into(ImageView imageView);
```

이후는 해당 함수만 표기합니다.

### Resize Image

```java
// Picasso
.resize(200, 200)

// Glide
.override(200, 200)
```

### Callback or Listener

```java
// Picasso
.into(ImageView imageView, Callback arg1);

// Glide
.listener(RequestListener<String, GlideDrawable> requestListener)
```

### Transform

```java
// Picasso
.transform(new CircleTransform())

// Glide
.transform(new CircleTransform(context))
```

### Thumbnail

```java
// Picasso
// No Support

// Glide
.thumbnail(0.1f)
```

### Animation

```java
// Picasso
// No Support

// Glide
.animate(anim)
```

### Placeholders

```java
// Picasso
.placeholder(drawable/resourceid)
.error(drawable/resourceid)

// Glide
.placeholder(drawable/resourceid)
.error(drawable/resourceid)
```

### 외부 통신 Library 연계

#### Picasso - OkHttp

```groovy
dependencies {
   compile 'com.squareup.picasso:picasso:2.4.0'
   compile 'com.squareup.okhttp:okhttp:2.4.+'
   compile 'com.squareup.okhttp:okhttp-urlconnection:+'
}
```


```java
OkHttpClient okHttpClient = new OkHttpClient();
Picasso picasso = new Picasso
   .Builder(this)
   .downloader(new OkHttpDownloader(okHttpClient)).build();
```

#### Glide - Vollery

```groovy
dependencies {
   compile 'com.github.bumptech.glide:glide:3.4.+'
   compile 'com.github.bumptech.glide:volley-integration:1.1.+'
   compile 'com.mcxiaoke.volley:library:1.0.+'
}
```


```java
Glide
   .get(this)
   .register(GlideUrl.class,
      InputStream.class,
      new VolleyUrlLoader.Factory(yourRequestQueue));
```

#### Glide - OkHttp

```groovy
dependencies {
   compile 'com.github.bumptech.glide:glide:3.4.+'
   compile 'com.github.bumptech.glide:okhttp-integration:1.1.+'
   compile 'com.squareup.okhttp:okhttp:2.4.+'
}
```


```java
Glide
   .get(this)
   .register(GlideUrl.class,
      InputStream.class,
      new OkHttpUrlLoader.Factory(yourRequestQueue));
```

_ _ _

## Debug

### Picasso

```java
// Picasso
Picasso.with(this).setLoggingEnabled(true);
```

#### Picasso Example : Cache Log
- created      [R1] Request{https://avatars2.githubusercontent.com/u/1534926?v=3&s=460}
- completed    [R0]+780ms from NETWORK
- completed    [R0]+362ms from DISK
- completed    [R1] from MEMORY

### Glide

Terminal에 다음을 입력

`adb shell setprop log.tag.GenericRequest <-loglevel->`

_ _ _

## Disk Cache

### Picasso

- LRU memory cache of 15% the available application RAM
- Disk cache of 2% storage space up to 50MB but no less than 5MB. (Note: this is only available on API 14+ or if you are using a standalone library that provides a disk cache on all API levels like OkHttp)
- Three download threads for disk and network access.

발번역
- 메모리 캐쉬, 어플리케이션 RAM의 15%를 사용
- 디스크 캐쉬, 전체 디스크의 최대 2%, 50MB 상한선 (API 14+ 혹은 OkHttp Library 사용시)


```java
new Picasso
   .Builder(this)
   .downloader(new OkHttpDownloader(okHttpClient)).build();
```

### Glide

Glide's disk cache is based on

상세내용 : https://github.com/bumptech/glide/wiki/Configuration

_ _ _

### 비교

| 비고 | Picasso | Glide |
| :-: | :-: | :--: |
| SDK Support | 9+ | 10+ |
| Memory Cache | 기본 | 기본 |
| Disk Cache | 선택 | 기본, 변경가능 |
| 이미지 다운로드 | O | O |
| 디코딩 | O | O |
| 이미지 후처리 | O | O |
| Animated GIF 지원 | X | O |
| Local video 지원 | X | O |
| Thumbnail | X | O |
| Animation | X | O |

_ _ _

### 지원 데이터 모델

| 비고 | conetnt:// | file:// | http:// | android.resource:// |
| :-: | :-: | :-: | :-: | :-: |
| Glide | O | O | O | O |
| Picasso | O | O | O | O |

- - -

Update

- 2015-01-19 14:58:00
 - 비교내용 Update
- 2015-01-16 11:25:00
 - Picasso, Glide 소개 페이지 Update
 - Picasso + OkHttp 연계 수정
 - Debug, Disk Cache 연결 추가

- - -

참고 사이트

1. http://vardhan-justlikethat.blogspot.kr/2014/09/android-image-loading-libraries-picasso.html
2. http://qiita.com/hotchemi/items/33ebd5faa42d2d05c2b6
3. http://qiita.com/hotchemi/items/375d63261f2eed2b18e1
4. https://spoqa.github.io/2014/03/28/android-picasso.html
5. http://helloworld.naver.com/helloworld/textyle/429368
