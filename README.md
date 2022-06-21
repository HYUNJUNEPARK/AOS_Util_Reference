# UsefulReferencelink


BaseFragment</br>
https://github.com/HYUNJUNEPARK/-Ref-AndroidUI/blob/main/3_ViewPager2_BottomNavigation/app/src/main/java/com/example/viewpager2_bottomnavigation/util/BaseFragment.kt </br>
-> **원하는 프래그먼트에 상속시킨 후 initView() 를 오버라이딩해 사용**

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


Permission</br>
https://github.com/HYUNJUNEPARK/-Ref-AndoridProgramming/blob/main/5_Permission/app/src/main/java/com/june/permission/Permission.kt </br>
-> **권한 요청이 필요한 Activity/Fragment 에서 `Permission(context).checkPermissions()` 호출, override onRequestPermissionResult**

```kotlin
//override onRequestPermissionResult
override fun onRequestPermissionsResult(requestCode: Int, permissions: Array<out String>, grantResults: IntArray) {
    super.onRequestPermissionsResult(requestCode, permissions, grantResults)

    //result : true(0) 모든 권한 승인 / false(-1) 권한 중 하나라도 거절
    if (grantResults.all { result -> result == PackageManager.PERMISSION_GRANTED}) { //권한을 모두 승인 받았을 때
        Permission(this).permissionGranted()
    }
    else { //권한 승인이 하나라도 거절되었을 때 -> AlertDialog
        Permission(this).permissionDenied()
    }
}
```


---

액션바 뒤로가기 버튼</br>
https://programmingworld1.tistory.com/15

Status bar 색상 변경</br>
https://origogi.github.io/android/dark-mode/

커스텀다이얼로그 데이터 바인딩</br>
https://github.com/HYUNJUNEPARK/BasicAOSApp/blob/main/1_BMICalculator/app/src/main/java/com/june/androidApp/bmicalculator/ResultDialog.kt

[Android, 단축키] 내가 자주 사용하는 단축키 정리 for MAC</br>
https://black-jin0427.tistory.com/186</br>
