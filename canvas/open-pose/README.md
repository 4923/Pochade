> 일단 영상이 아니라 이미지를 기준으로 하고 실시간 영상처리는 어떻게 해결해야할지 언젠가 고민을 해보자


- [ ] <s>google pose detection API 끌어오기</s> Pose Detection
    - [네이버 클라우드 플랫폼](https://www.ncloud.com/product/aiService/poseEstimation) 에서 결제수단 등록 후 사용 가능 - 호출마다 돈들어가서 그냥 Open Pose 사용하기로 함
    - [ ] OpenPose 튜토리얼

---

## Starters-Kit

1. 설치

```bash
# 빌드 폴더 생성
mkdir openpose-build
cd openpose-build

# 설치
git clone https://github.com/CMU-Perceptual-Computing-Lab/openpose.git
cd openpose
```

이제부터 ~/openpose 를 루트경로라고 부름

2. 종속성 설치

```bash
루트경로/scripts/osx/install_deps.sh
```


지금

```
==> Running `brew cleanup viennacl`...
Disable this behaviour by setting HOMEBREW_NO_INSTALL_CLEANUP.
Hide these hints with HOMEBREW_NO_ENV_HINTS (see `man brew`).
sudo -H python2 -m pip install numpy<1.17
Password:
sudo: python2: command not found
sudo -H python2 -m pip install opencv-python<4.3
sudo: python2: command not found
```
여기서 에러났는데 어떡하냐
어차피 마지막 줄이라

```bash
# install_deps.sh

pip_packages="numpy<1.17 opencv-python<4.3"
for pkg in $pip_packages
do
    echo "sudo -H python3 -m pip install $pkg"
    sudo -H python3 -m pip install "$pkg"
done
```

python2을 python3으로 변경 Mac에서 python 기본은 2이고, python3 을 쓸 때 버전을 붙여야 하므로)
sudo 명령어가 있으므로 중간에 비밀번호 치라고 함
python3으로 변경했을 때 설치되는게 있으니 가능하면 변경할 것

3. caffe 설치

```bash
brew install caffe
```

TIL 참고해서 cmake-gui 설치까지 완료

4. cmake용 빌드 설정 configure: `cmake-gui`

3에서 제대로 설치했으면 터미널에 `cmake-gui` 입력했을때 창 하나 뜸
- 프로젝트 경로: 루트경로
- 빌드 경로: 루트경로/build

설정 후 `configure` 실행 -> 이후에 뜨는 창은 기본값으로

<img width="903" alt="image" src="https://user-images.githubusercontent.com/60145951/175660597-82318186-91ac-446e-832b-ed0b0070e73a.png">

설치 완료되면 이렇게 빨간색으로 뜨는데

```
[√] BUILD_CAFFE 
[ ] BUILD_EXAMPLES 
[ ] BUILD_SHARED_LIBS 
Caffe_INCLUDE_DIRS = <루트 경로> /build/caffe/include 
GPU_MODE = OPENCL
```

이부분만 수정한다 

<img width="903" alt="image" src="https://user-images.githubusercontent.com/60145951/175660864-4874bb12-4eb4-40eb-a13c-3c0702e0ca17.png">

이상태로 configure 하면 빨간색이 사라진다

<img width="1036" alt="image" src="https://user-images.githubusercontent.com/60145951/175665680-4a73198d-09dd-4508-9c56-efc4839161f8.png">


generate 하여 단계를 완료한다.

<img width="869" alt="image" src="https://user-images.githubusercontent.com/60145951/175661266-09ed3ba3-6256-420b-832a-25fd95c086fd.png">

### issue: cblas.h not found

<img width="937" alt="image" src="https://user-images.githubusercontent.com/60145951/175665183-a7a7b60d-9e8f-434a-a207-09460b89cd6d.png">

- https://stackoverflow.com/questions/40668588/cblas-h-error-while-installing-caffe-on-mac-os
- https://github.com/BVLC/caffe/issues/3599
- https://letni.tistory.com/18

등에서 `open-blas` 안에 `cblas.h`가 포함되어 있으니 이를 설치하라고 조언하는데 `brew install openblas` 을 해도 같은 문제가 발생한다. conda와 충돌이 있다는 말도 있어 꺼봤지만 달라지지는 않았다. 

brew --prefix 로 확인한 위치에 cblas가 있는데 불러오지 못하는 것 같다.

<img width="725" alt="image" src="https://user-images.githubusercontent.com/60145951/175668020-453e793a-2e71-45ae-86b7-738d77c24ad0.png">

[여기](https://github.com/amd/OpenCL-caffe/issues/36#issuecomment-199587164)서 말하는 바에 따르면 brew로 설치한 경우 주석 처리를 제거해야 한다는데 `Makefile.config`를 어디서 찾을지 모르겠다. == [build dir의 최상위에 있다고 한다.](https://releases.llvm.org/1.6/docs/MakefileGuide.html#:~:text=Makefile.config-,Every%20project%20must%20have%20a%20Makefile.,of%20the%20project's%20source%20directory.) .config 가 없는 Makefile을 말하는 것 같아 편집하기로 했다. 


