# HandiLab Projcect
## 2022.05~2022.07

* AR을 활용한 과학실험 앱(고등 교육과정 반영) <br/>
- 항생제 내성균 실험 <br/>
- 멸치 해부 실험 <br/>
- 추후 추가 실험 개발 예정 <br/>

- 각 Scene 설명 <br/>
- MainScene <br/>
각 실험 버튼을 Scroll Rect를 활용해 구성 <br/>
원하는 실험의 버튼을 클릭 <br/>
- 각 실험의 공통된 작업 <br/>
스마트폰 카메라를 통해 평면을 인식하면 실험 테이블이 놓일 위치를 미리 보여줌 <br/>
원하는 곳에 위치시킨 후 터치하면 테이블이 고정되어 위치함 <br/>
테이블이 놓인 후 실험 시작 <br/>
- Exp1 <br/>
텍스트, 음성 가이드에 따라 실험을 진행 <br/>
1. Scene1 <br/>
스포이트를 드래그하여 배지 위에 옮겨놓은 후 손잡이 부분을 터치해 각 배지에 배양액을 떨어트린다 <br/>
2. Scene2 <br/>
유리봉을 드래그하여 일반배지부터 각 배지를 도말한다 <br/>
여기서 일반배지가 아닌 항생제 배지를 먼저 도말하려고 할 경우 일반배지가 깜빡이는 효과를 넣었다 <br/>
3. Scene3 <br/>
각 배지를 터치해 뚜껑을 덮은 후 터치를 통해 각 배지를 배양기에 옮겨넣는다 <br/>
이 Scene은 모든 상호작용을 터치+애니메이션으로 제작했다 <br/>
배양기 문이 열려있지 않은 상태에서 배지를 옮기려고 할 경우 배양기가 깜박이는 효과를 넣었다 <br/>
4. Scene4 <br/>
FadeIn, FadeOut 활용, 시계가 돌아가는 애니메이션을 통해 1일 후를 표현 <br/>
배양기를 터치하여 배양기 문을 열고 배지를 가져온다 <br/>
테이블에 놓고 관찰할 수 있고, 화면을 터치하면 실제 결과 사진을 보여준다 <br/>
- Exp2 (멸치 해부 실험)
텍스트, 음성 가이드에 따라 실험을 진행 <br/>
1. Scene0 <br/>
핀셋을 드래그하여 비커 위로 옮기면 멸치가 비커 속으로 들어간다
불리는 게이지가 다 차면 핀셋을 터치하여 멸치를 건져올린다(게이지가 차기 전엔 핀셋의 Lean 컴포넌트를 꺼놓아 움직이지 못 하게 함)
테이블 위의 슬라이드 글라스에 불린 멸치를 옮겨놓는다
2. Scene1 <br/>
핀셋을 드래그하여 멸치의 겉부분을 걷어낸다
드러난 멸치의 장기와 유문수를 각각 드래그하여 떼어낸다
위를 터치하여 패트리 접시에 옮겨 담는다
멸치의 위가 너무 작기 때문에 편의를 위해 패트리 접시를 터치하여 확대되도록 했다
3. Scene2 <br/>
메스를 드래그하여 위를 가른다 <br/>
4. Scene3 <br/>
가른 위를 터치하여 스포이트가 생기면 손잡이 부분을 터치해 물을 떨어트린다 <br/>
5. Scene4 <br/>
스포이트를 드래그하여 위 내부의 내용물에 닿게 한 후 손잡이 부분을 터치해 빨아들인다 <br/>
스포이트를 드래그하여 슬라이드 글라스 위에 놓인 후 손잡이 부분을 터치해 몇 방울 떨어트린다 <br/>
슬라이드 글라스를 터치하여 커버 글라스를 씌운 후 또 한 번 터치해 현미경에 넣는다 <br/>
현미경을 터치해 실제 결과 화면을 볼 수 있다 <br/>

시행착오 <br/>
- 실험 테이블을 놓을 때 처음 앱을 시작할 때의 방향으로만 놓여지는 문제 <br/>
스마트폰 카메라의 방향을 실시간으로 받아와서 그 방향을 Spawn object에 반영해주면 해결할 수 있을 것이라 생각 <br/>
//실제 해결 방안 작성// <br/>
PlacementIndicator.cs
```c++
private void PlaneIndication()
    {
        var screenCenter = ARCam.ViewportToScreenPoint(new Vector3(0.5f, 0.5f));

        if (arRaycastManager.Raycast(screenCenter, hits, TrackableType.All) && spawnedObject == null)
        {
            Pose hitPos = hits[0].pose;

            var cameraForward = ARCam.transform.forward;
            var cameraBearing = new Vector3(cameraForward.x, 0, cameraForward.z).normalized;

            hitPos.rotation = Quaternion.LookRotation(cameraBearing);
            ARIndicator.SetActive(true);
            ARIndicator.transform.SetPositionAndRotation(hitPos.position, hitPos.rotation);
            placementPoseIsValid = hits.Count > 0;

            if (spawnedObject == null && placementPoseIsValid && Input.touchCount > 0 && Input.GetTouch(0).phase == TouchPhase.Began)
            {
                spawnedObject = Instantiate(arObjectToSpawn, ARIndicator.transform.position, Quaternion.LookRotation(cameraBearing));
                guideCanvas.SetActive(false);
            }

        }
        else
        {
            ARIndicator.SetActive(false);
        }
    }
```
ARCam(스마트폰 카메라)의 정면 방향을 받아와서 x축과 z축을 cameraBearing에 저장
```
var cameraForward = ARCam.transform.forward;
            var cameraBearing = new Vector3(cameraForward.x, 0, cameraForward.z).normalized;
```
그 후 hitPose의 rotation을 ARCam이 보는 방향을 보도록 설정한다
```
hitPos.rotation = Quaternion.LookRotation(cameraBearing);
```
- 멸치 해부 실험에서 핀셋을 드래그하여 비커 위로 옮길 때 3개의 축이 모두 움직여지기 때문에 z축으로 인해 핀셋이 비커 뒤로 가게 되는 문제 <br/>
z축이 움직여질 필요는 없기 때문에 z축을 고정해주면 해결할 수 있을 것이라 생각 <br/>
//실제 해결 방안 작성// <br/>
Z_Control.cs <br/>
```
public Vector3 startVec;

    // Start is called before the first frame update
    void Start()
    {
        startVec = gameObject.transform.localPosition; // 처음 z값
    }

    // Update is called once per frame
    void Update()
    {
        Vector3 temp = gameObject.transform.localPosition;
        temp.z = startVec.z;         // temp를 계속 현재 gameObject의 위치로 바꿔주되, z는 startvec으로 설정 -> 이 녀석을 게임오브젝트의 위치로 설정
        gameObject.transform.localPosition = temp;

    }
```

사용목적 <br/>


활용방안 <br/>



