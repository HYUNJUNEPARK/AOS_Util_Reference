# AOS Util Reference
---

1. <a href = "#1">권한</a></br>
2. <a href = "#2">바인딩</a></br>
  2.1. dataBinding</br>
  2.2. viewBinding</br>
3. <a href = "#3">뷰모델</a></br>
  3.1 ViewModel에서 Context 필요할 때</br>
  3.2 LiveData</br>
  3.3 Observer</br>
4. <a href = "#4">앱 재시작 코드</a></br>

<a href = "#ref">참고링크</a></br>  

---

><a id = "1">1.권한</a>

```kotlin
private val permissionRequestCode = 999

//필요 권한 리스트
private val permissionsArray: Array<String> = arrayOf(
    Manifest.permission.READ_CONTACTS
)

//권한 요청이 필요한 Activity/Fragment 에서 호출
fun checkPermissions() {
    //API 23 이상
    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M) {
        val isAllPermissionGranted: Boolean = permissionsArray.all { permission ->
            context.checkSelfPermission(permission) == PackageManager.PERMISSION_GRANTED
        }
        if (isAllPermissionGranted) {
            permissionGranted()
        } else {
            ActivityCompat.requestPermissions(
                context as Activity,
                permissionsArray,
                permissionRequestCode
            )
        }
    } else {
      //API 23 미만 
    }
}

//override
override fun onRequestPermissionsResult(requestCode: Int, permissions: Array<out String>, grantResults: IntArray) {
    super.onRequestPermissionsResult(requestCode, permissions, grantResults)

    if (grantResults.all { it == PackageManager.PERMISSION_GRANTED}) {
        //권한을 모두 승인 받았을 때
        permission.permissionGranted()
    }
    else { 
        //권한 승인이 하나라도 거절되었을 때
        permission.permissionDenied()
    }
}

//메서드 내부에서 권한 체크 시 사용
if (PermissionChecker.checkSelfPermission(context, Manifest.permission.CALL_PHONE)
    == PermissionChecker.PERMISSION_GRANTED
) {
    //권한 승인 시
} else {
    //권한 미승인 시
}
```
<br></br>


><a id = "2">2.바인딩</a></br>

2.1.dataBinding
```kotlin
class MainActivity : AppCompatActivity() {
    private lateinit var binding: ActivityMainBinding

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = DataBindingUtil.setContentView(this, R.layout.activity_main)
    }
}

//XML
//editText.text XML에서 바로 String 파라미터로 사용
android:onClick="@{()->callViewModel.makeCall(editText.getText().toString())}"


class EcdhFragment : Fragment() {
    private var _binding: FragmentEcdhBinding? = null
    private val binding get() = _binding!!

    override fun onCreateView(inflater: LayoutInflater, container: ViewGroup?, savedInstanceState: Bundle?): View? {
        _binding = DataBindingUtil.inflate(inflater, R.layout.fragment_ecdh, container, false)
        return binding.root
    }

    override fun onDestroyView() {
        super.onDestroyView()
        _binding = null
    }
}

```

2.2.viewBinding
```kotlin
class MainActivity : AppCompatActivity() {
    private lateinit var binding: ActivityMainBinding

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityMainBinding.inflate(layoutInflater)
        setContentView(binding.root)
    }
}
```
<br></br>


><a id = "3">3.뷰모델</a></br>

3.1 ViewModel에서 Context 필요할 때
```kotlin
class MyViewModel(application: Application) : AndroidViewModel(application) {
    private val context = getApplication<Application>().applicationContext

}    
```

3.2 LiveData
```kotlin
private var _isUserInfoFetching = MutableLiveData<Boolean>()
val isUserInfoFetching: LiveData<Boolean>
    get() = _isUserInfoFetching
```

3.3 observer
```kotlin
userInfoViewModel.isUserInfoFetching.observe(this){ isFetching ->

}
```

<br></br>


><a id = "4">4.앱 재시작 코드</a></br>
```kotlin
val packageManager: PackageManager = packageManager
val intent: Intent = packageManager.getLaunchIntentForPackage(packageName)!!
val componentName: ComponentName? = intent.component
val mainIntent = Intent.makeRestartActivityTask(componentName)
startActivity(mainIntent)
exitProcess(0)
```
<br></br>






BaseFragment</br>
https://github.com/HYUNJUNEPARK/-Ref-AndroidUI/blob/main/3_ViewPager2_BottomNavigation/app/src/main/java/com/example/viewpager2_bottomnavigation/util/BaseFragment.kt </br>
-> **원하는 프래그먼트에 상속시킨 후 initView() 를 오버라이딩해 사용 (dataBinding 사용)**</br>
```kotlin
//상속 예시
class AFragment : BaseFragment<FragmentABinding>(R.layout.fragment_a) {
    override fun initView() {
        super.initView()

        binding.apply {

        }
    }
}
```
<br></br>




NetworkConnection</br>
https://github.com/HYUNJUNEPARK/-Ref-AndoridProgramming/blob/main/8_NetworkConnection/app/src/main/java/com/example/networkstate/NetworkConnectionCheckModule.kt </br>
-> **Activity onCreate/onDestroy 에 register()/unregister() 해 사용 (생명주기에 맞게 호출해 사용)**</br>

```kotlin
class MainActivity : AppCompatActivity() {
    private val networkCheck: NetworkConnectionCallback by lazy { NetworkConnectionCallback(this) }
    
    override fun onCreate(savedInstanceState: Bundle?) {
        //...
        networkCheck.register()
    }
    
    override fun onDestroy() {
        //...
        networkCheck.unregister()
    }
}    
```
<br></br>


AlertDialog</br>
-경고창, 팝업창 구현 시 사용</br>
```
AlertDialog.Builder(context)
    .setTitle("권한 설정")
    .setMessage("권한 거절로 인해 일부기능이 제한됩니다.")
    .setPositiveButton("취소") { _, _ -> }
    .setNegativeButton("권한 설정하러 가기") { _, _ ->
        applicationInfo()
    }
    .create()
    .show()
```
<br></br>



Thread</br>
```kotlin
fun showThreadName() {
    val threadName = Thread.currentThread().name
    println("Running on thread: $threadName")
}
```
<br></br>


DataCass -> JSON -> String</br>
```kotlin
Gson().toJson(
    ErrorResponse(
        ExecStatus(
            code = response.code().toString(),
            message = message
        )
    )
)
```
<br></br>


JSONObject에서 원하는 데이터 추출</br>
```kotlin
fun extractResponseCode(preferencesResponse: String): String {
    return try {
        JSONObject(preferencesResponse).getJSONObject(EXECSTATUS).getString(CODE)
    } catch (e: Exception) {
        e.printStackTrace()
        handleExceptionResponse(e.toString())
    }
}
```
<br></br>


build.gradle(.app) buildType 세팅 -> Sync Project with Gradle Files</br>
```kotlin
buildTypes {
    debug {
        buildConfigField "String", "BASE_URL", "\"http://1-.2-.3-.7-:5---/\""
        buildConfigField "Boolean", "DEBUG_MODE", "true"
    }
    release {
        //...
        buildConfigField "String", "BASE_URL", "\"http://2--.7-.2--.7-:5---/\""
        buildConfigField "Boolean", "DEBUG_MODE", "false"
    }
}
```
<br></br>


invoke()</br>
-'opertor'와 함께 invoke() 함수를 정의하여 클래스 인스턴스를 함수처럼 호출할 수 있다.</br>
```kotlin
//invoke 가 사용된 use case 예시
class FormatDateUseCase(userRepository: UserRepository) {
    private val formatter = SimpleDateFormat(
        userRepository.getPreferredDateFormat(),
        userRepository.getPreferredLocale()
    )
    
    operator fun invoke(date: Date): Stirng {
        return formatter.format(date)
    }
}


//use case 호출 예시
class MyViewModel(formatDataUseCase: FormatDateUseCase): ViewModel() {
    init {
        val today = Calendar.getInstance()
        val todayDate = formateDateUserCase(today)
    }
    //...
}
```


---
><a id = "ref">참고 링크</a>

AAR</br>
Library AAR 파일 생성</br>
https://cording-cossk3.tistory.com/156</br>

프로젝트 구조 대화상자를 사용하여 종속 항목 추가</br>
https://developer.android.com/studio/projects/android-library?hl=ko#psd-add-library-dependency</br>
cf).aar 파일을 Project/app/libs 에 복사</br>
-> File > Project Structure > Dependencies > Add Jar/Aar Dependency > Step 1 Provide a path to the library file or directory to add 'libs/aar파일명'</br>

BuildConfig.DEBUG가 릴리즈 모드에서도 true가 되는 현상</br>
https://satisfactoryplace.tistory.com/147</br>

파이어베이스 gradle 세팅 방법(gradle 7.x 이상)</br>
https://gift123.tistory.com/68</br>
https://developer.android.com/studio/releases/gradle-plugin?hl=ko#updating-plugin</br>

프래그먼트 화면 전환 시 상태 유지하기 (FragmentManager)</br>
https://hanyeop.tistory.com/425</br>

Android) Fragment 에서 View Binding 문제점, 제대로 사용하기</br>
https://yoon-dailylife.tistory.com/57</br>

AOS 외부 앱 실행</br>
https://www.fun25.co.kr/blog/android-execute-3rdparty-app/?category=003</br>

Android - PackageManager로 Package 정보 가져오기</br>
https://codechacha.com/ko/android-query-package-info/</br>

파이어베이스 그레들리 셋팅 방법(buildscript 설명 참고)</br>
https://gift123.tistory.com/68</br>

Kotlin Enum Class Ex</br>
https://blog.logrocket.com/kotlin-enum-classes-complete-guide/</br>

앱 재시작 코드</br>
https://stackoverflow.com/questions/6609414/how-do-i-programmatically-restart-an-android-app</br>
