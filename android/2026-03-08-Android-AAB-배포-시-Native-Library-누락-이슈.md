### Context
안드로이드 앱을 구글 플레이스토어에 배포한 후, 특정 기능이 작동하지 않는 현상이 발생했습니다. 로컬 디버그 빌드(Debug APK)에서는 정상적으로 작동했지만, 프로덕션용 AAB(Android App Bundle)로 빌드하여 배포한 버전에서만 문제가 나타났습니다. 초기에는 코드 난독화 도구인 R8/ProGuard의 설정 문제로 의심했으나, 원인은 AAB 빌드 시 네이티브 라이브러리(.so 파일)가 제대로 패키징되지 않은 것에 있었습니다.

### Core
Android App Bundle(AAB)은 앱 크기를 최적화하기 위해 필요한 자원만 추출하여 설치하는 방식을 사용합니다. 네이티브 라이브러리를 포함하는 앱의 경우, 간혹 기기 아키텍처에 따른 라이브러리 누락이나 패키징 이슈가 발생할 수 있습니다. 이를 해결하기 위해 `build.gradle.kts` 파일 내 `packaging` 설정을 통해 기존의 APK 패키징 방식을 강제할 수 있습니다.

```kotlin
android {
    // ... 기존 설정 ...

    packaging {
        jniLibs {
            // AAB 배포 시 네이티브 라이브러리(.so)가 포함되지 않는 문제를 방지
            useLegacyPackaging = true
        }
    }
}
```

### Insight
이 문제는 AAB가 기기별로 최적화된 APK를 생성(Dynamic Delivery)하는 과정에서 네이티브 라이브러리(.so)가 명시적으로 포함되지 않을 때 발생합니다. `useLegacyPackaging = true` 옵션은 네이티브 라이브러리를 압축하지 않고 APK 파일 내에 그대로 포함시키는 설정입니다. 배포 후 기능이 누락되는 문제를 방지하기 위해 네이티브 라이브러리를 사용하는 프로젝트라면 배포 전 반드시 `bundleRelease`로 생성된 AAB가 올바르게 패키징되었는지 확인해야 합니다. 

**출처:** [Android App Bundle 및 네이티브 라이브러리 패키징](https://developer.android.com/build/build-variants#native-libraries), [useLegacyPackaging 설정 공식 문서](https://developer.android.com/reference/tools/gradle-api/8.0/com.android.build.api.dsl.JniLibsPackagingOptions#useLegacyPackaging)