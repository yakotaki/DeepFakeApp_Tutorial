#DeepFake 사용법  
----
## 1. DeepFake란?
* 레딧에서 누군가 딥러닝 기반의 얼굴을 바꿔주는 툴을 만들었다. 그런데 또 다른 누군가 그것을 이용하여 GUI Tool 을 만들었다. 딥러닝이란 머신러닝의 일종으로 신경망을 본따 구조를 만들어 최근 패턴파악에 널리 쓰이고 있는 기술이다.  
* A사진과 B사진을 학습하여 최대한 비슷하게 만들어주는 신경망으로 학습하고 이를 실제 영상 프레임에 적용하고 프레임을 모두 영상으로 이어준다면 DeepFake가 되는 것이다.
## 2. 실행하기에 앞서..  
* 위에서 A사진과 B사진이라고 이야기했는데 사실 영상은 사진들의 조합이다. 그렇다. 영상을 모두 사진으로 분리해준다면 두 영상으로 학습하여 A영상 속의 얼굴을 B영상 속의 얼굴로 바꿔치기 할 수 있다는 것이다.
* DeepFake는 GPU(그래픽카드)가 필요하다. 없다면 상당한 시간이 소요된다. 그냥 못한다고 보는게 편하다. 그리고 좋은 GPU일 수록 좋고, 컴퓨터에 CUDA 8.0  설치해야한다. 설치는 [여기서](https://developer.nvidia.com/cuda-80-ga2-download-archive).
* 필요한 것은 두 영상과 DeepFakeApp 소스파일이다. 두 영상은 알아서 구하도록하고 DeepFakeApp은 [여기서](https://shorturl.at/pyIO6) 받을 수 있다.(2018/01/13 다운로드 가능)
* 그리고 영상을 사진으로 나눠주기 위해 FFMPEG가 필요하다. [다운로드](https://www.ffmpeg.org/download.html) 를 받고 **환경변수를 설정**해야하는데 하기 귀찮으니까 그냥 다운받은거 한폴더에 다 풀어주도록 하자.
* ~~그리고 아마 어떤 에러가 뜰 것인데 그것도 바로 해결해주기 위해 [이것]() 도 다운받자~~(1.1버전에서 해결)
* DeepFakeApp의 레딧은 [여기](https://www.reddit.com/r/deepfakes/comments/7ox5vn/fakeapp_a_desktop_tool_for_creating_deepfakes/)
## 3. 영상을 사진으로 분리
* 이제 영상을 분리해서 사진으로 만들어줘야 하는데 앞서 언급한대로 FFMPEG를 사용할 것이다.
* 우선 폴더를 하나 만들자 필자는 train 이라는 폴더를 만들었다. 그리고 그 안에 영상하나를 넣어주고 이름을 바꿔주자 필자는 scene.mp4로 변경하였다. 그리고 영상의 프레임레이트를 알아야하는데 편법으로 알아보도록 하자.
* 윈도우를 기준으로 설명하자면 윈도우 탐색기를 열고 다운로드 받은 폴더를 열고 흰색 바탕에 Shift 키를 누른 상태로 우클릭을 하면 **여기에 PowerShell 창 열기** 가 있을 것이다.(윈도우 10 기준) 그것을 누르면 퍼런색 창이 하나 뜨게 되는데 여기서 이제 주의하자 환경변수를 설정하지 않았기 때문에 주소가 상당히 헷갈릴 수 있다. 우리가 지금 퍼런색 창을 띄운 곳은 우리가 다운로드 받은 폴더들을 모두 넣은 바로 그 폴더이다. 자. 여기에 ffmpeg를 다운받고 압축을 풀었을텐데 그렇다면 현재폴더에서 ./ffmpeg/bin 안에 exe 들이 있어야한다. 없다면 아래 입력할 때 맞춰주어야한다. 우선 ./ffmpeg/bin 에 exe 들이 있다고 가정하겠다. 이제 퍼런색 창에다가 아래 명령문을 복붙하고 엔터를 치자.
>./ffmpeg/bin/ffmpeg.exe -i ./train/scene.mp4 -vf fps=[FPS OF VIDEO] "./train/out%d.png"
* 그러면 이제 오류가 나면서 멈추게 되는데 여기서에 프레임레이트를 볼 수 있다.
>Stream #0:0(und): Video: h264 (Main) (avc1 / 0x31637661), yuv420p(tv, bt709), 1280x720 [SAR 1:1 DAR 16:9], 974 kb/s, 29.97 fps, 29.97 tbr, 90k tbn, 59.94 tbc (default)
* 위에 오류난 부분을 보면 Stream 파트에 29.97 fps를 볼 수 있는데 그것이 프레임 레이트이다. 이것은 영상마다 다르기 때문에 바꿔줘야한다. 다시 명령문으로 돌아가서 [FPS OF VIDEO] 를 숫자로 바꿔준다. 필자의 경우는 아래와 같다.
>./ffmpeg/bin/ffmpeg.exe -i ./train/scene.mp4 -vf fps=30 "./train/out%d.png"
* 이제 변환을 시작하고 완료가 되고 그 폴더에 들어가면 엄청난 그림파일들이 쏟아질 것이다.
* 또 다른 폴더를 만들고 위 항목을 반복한다. 필자는 train2 라는 폴더를 만들고 위 항목을 반복했다. train 폴더는 A train2 폴더는 B 라고 명명하겠다.
## 4. 얼굴 추출
* 이제 다운로드 받은 FakeApp의 **"fakeapp.bat"** 을 실행한다.
* 처음 실행시 어떤 파일을 다운로드 받을텐데 몇분 기다리면 다운로드가 완료되고 FakeApp 이라는 창이 뜰 것이다. 그 위에는 Extract / Train / Conv... 이 있는데 Extract 는 얼굴을 뽑아내는 파트, Train은 뽑아 낸 얼굴을 학습하는 파트 Conv... 는 학습한 것을 바탕으로 얼굴을 바꾸는 파트이다.
* 먼저 Extract 에서 Paths Data: 를 아까 사진으로 추출 했던 train 폴더의 경로를 입력하고 (절대경로) Start를 눌러 얼굴을 추출한다. (경로를 입력할 때 \ 백슬래시가 아닌 / 슬래시로 입력한다.) 완료되면 마찬가지로 train2 도 작업해준다.  
* 작업이 완료되면 train 폴더 안에 aligned 폴더가 생성되었을 것이다. 그 폴더안에는 얼굴만 잘려 저장되어있다.  
## 5. 학습
* 이제 두번째 파트인 Train으로 들어가서 Paths 에 Model을 다운로드 받은 FakeApp 의 Model 폴더의 경로를 입력해준다.
* Data A에는 train의 폴더 안에 있는 aligned 폴더를 입력해준다. Data B에는 train2의 aligned를 넣어준다.
* 옵션으로 Batch Size와 Save Period 가 있는데 Batch Size란 학습할 때 모든 얼굴을 불러와서 학습하기에는 시간과 자원이 너무 많이 들기 때문에 임의로 얼굴 몇개를 불러와서 학습을 하게 되는데 한번에 몇개씩 불러올 것인지를 나타내는 파라미터이고 Period는 학습한 것을 얼마만에 한번 저장할 것인지를 나타내는 파라미터이다. 나중에 더 좋은 퀄리티를 얻기위해 조절하며 실험해보면서 터득할 수 있다. (저장 Period는 퀄리티에 영향은 없지만...)  
* Model Processor는 GPU를 사용하자. 없다면 사서 꼽도록하자.
* Layers는 신경망의 계층을 몇단으로 구성할 것인지에 대한 파라미터이고 Nodes는 한 레이어에 몇개의 노드를 둘 것인지에 대한 파라미터이다. 마찬가지로 다음에 실험하며 터득하자.
* Mem Ratio와 GPU Growth는 우선 default와 false로 놔두도록 하자. 그리고 Start를 눌러준다.
* 어떤 검은색 창이 뜨면서 학습을 시작하는데 오래 둘수록 학습시간이 길어져 더 좋은 퀄리티가 나올 가능성이 높아지지만 애초에 파라미터 설정이 잘못되면 OverFit 되어 예상하지 못한 결과가 나온다. 학습된 모델은 아까 FakeApp의 Model 폴더 안에 저장되며 나중에 그 모델로 사용하면 예전에 학습했던 그 모델을 불러와 다시 학습하기 때문에 원하는 결과에 더 빨리 도달할 수 있다.(물론 OverFit 되지 않았다는 가정하에...)
* Train은 지루한 과정이지만 그냥 컴퓨터 켜두고 자거나 하루 냅두면 어느정도 완료되었을 것이다. 얼굴이 박힌 창에 마우스를 한번 클릭해주고 q를 누른다음 꺼주도록하자. 그럼 현재까지 한 것이 저장되며 Train은 완료되었다.
## 6. 얼굴 바꿔주기
* 세번째인 Conv...로 가자. Model은 위에서 입력한 것과 동일하고 Data는 얼굴을 바꿔줄 사진들이 있는 train 폴더를 입력하자. 그리고 train2 의 aligned 폴더를 train 폴더에 있는 aligned 폴더와 바꿔준다. 아래 옵션은 굳이 설명하지 않아도 알 것이라고 생각한다. 이제 Start 를 누르고 완료가 되면 train 폴더 안에 Merged 라는 폴더가 생기고 얼굴이 바뀐 사진들이 생겼을 것이다. 이제 이것을 영상으로 바꿔줄 것이다.
* 위에서 열었던 파란색 창에 다음을 입력하고 엔터를 누른다.
> ./ffmpeg/bin/ffmpeg.exe -r [FRAME RATE] -f image2 -s 1920x1080 -start_number 1 -i train/merged/out%d.png -vframes 1000 -vcodec libx264 -crf 25  -pix_fmt yuv420p test.mp4
* 몇가지 바꿔줄 것이 있는데 [FRAME RATE] 를 아까 train 의 scene.mp4의 fps를 입력해주고 -s 1920x1080 에는 해상도를 입력해주고 -vframes 다음의 1000은 train 폴더의 out****.png 의 마지막 번호를 입력해준다. 나머지는 구글에 검색해보면 무엇을 의미하는지 알 수 있을 것이다. -crf는 숫자가 낮을 수록 퀄리티가 높아진다고 한다. 우선 건들지말고 만들고 나중에 터득하도록 하자.
* 합치기가 완료되면 train/merged 폴더에 test.mp4가 생성되었는데, 틀어보도록 하자. 원하는 결과가 나왔는가? 아니라면 다시 학습파트로 돌아가 학습을 길게하거나 파라미터들을 바꿔주며 원하는 결과를 얻을 때 까지 노력하도록 하자.

## 7. 마무리
* 좋은 곳에 사용하시길 바라며 이 글은 단지 레딧에 있는 소스를 사용하는 방법을 알려줄 뿐 절대 악용하라는 소리는 하지 않았고 이것을 이용해서 만들어진 것들과 필자는 전혀 관련이 없으며 책임지지 않음을 밝힙니다.
