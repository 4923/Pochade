<div align="center">

<img src="https://user-images.githubusercontent.com/60145951/175774283-a354121c-2404-4932-a65a-47e466a9fb31.png" alt="Conference on Computer Vision and Pattern Recognition" width=300>

<small>Conference on Computer Vision and Pattern Recognition</small>

</div>

- **Input**: Image, video, **webcam**, Flir/Point Grey, IP camera, and support to add your own custom input source (e.g., depth camera).
- **Output**: Basic image + keypoint display/saving (PNG, JPG, AVI, ...), **keypoint saving** (JSON, XML, YML, ...), keypoints as array class, and support to add your own custom output code (e.g., some fancy UI).
- **OS**: Ubuntu (20, 18, 16, 14), Windows (10, 8), **Mac OSX**, Nvidia TX2.
- **Hardware compatibility**: CUDA (Nvidia GPU), OpenCL (AMD GPU), and **non-GPU (CPU-only)** versions.

- custom
    - 로컬에서 값 추출되는 정도만 확인하려 했으므로 CPU-only로 세팅
    - OpenCL이 좀 더 성능이 좋으나 설치에 어려움이 따른다고 함. 둘 다 시도해봤으나 clang compile 문제로 현재 중지상태

---

### 개발방법

- 윈도우에서 바로 사용할 수 있는 방법 (portable kit) 이 있으나 맥인 관계로 소스코드 받아와 직접 실행하는 과정 거침: [https://github.com/CMU-Perceptual-Computing-Lab/openpose/blob/master/doc/installation/0_index.md#compiling-and-running-openpose-from-source](https://github.com/CMU-Perceptual-Computing-Lab/openpose/blob/master/doc/installation/0_index.md#compiling-and-running-openpose-from-source)
- clang 컴파일은 Xcode를 통해 할 수 있다는 이야기가 나오는데 (SDK 에러메시지) 맥북이 XCode를 거부하는지 반대인지 설치가 안된다…
    
    ![구글 자동검색도 있던데 수동설치는 또 안됨… 강제로 지워야 하는 폴더가 없어서…](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/2be0c496-6520-4e77-bfb4-38df40b80d15/Untitled.gif)
    
    구글 자동검색도 있던데 수동설치는 또 안됨… 강제로 지워야 하는 폴더가 없어서…

---
### Install Log

> [ref: Medium / Build openpose with/without GPU support for macOS (Catalina 10.15.6) / 2020.8](https://medium.com/@alok.gandhi2002/build-openpose-with-without-gpu-support-for-macos-catalina-10-15-6-8fb936c9ab05)

### 1. 설치

```bash
# 빌드 폴더 생성
mkdir openpose-build
cd openpose-build

# 설치
git clone https://github.com/CMU-Perceptual-Computing-Lab/openpose.git
cd openpose
```

이제부터 ~/openpose 를 루트경로라고 부름

### 2. 종속성 설치: 무시해도 OK

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
에러 발생해서 아래와 같이 변경

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

### 3. caffe 설치

```bash
brew install caffe
```

TIL 참고해서 cmake-gui 설치까지 완료

### 4. cmake용 빌드 설정 configure: `cmake-gui`

> [NOTE] 이후 이슈들에 적어두었지만 최종 빌드 완료된 설정은 하단 스크린샷과 같음. 중간중간 변경해야 하는 지점이 있고 한번에 위와 같이 세팅한다고 끝나는게 아니니 막힐때마다 구글링할 것. 가장 유의해야 할 점은 각 파일들의 위치가 어디인지 확인하고 수정하는 것.
> <img width="1000" alt="image" src="https://user-images.githubusercontent.com/60145951/175809559-db865114-f840-48c7-bd7f-1be198e7fd5a.png">


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
Caffe_INCLUDE_DIRS = <루트 경로>/build/caffe/include 
GPU_MODE = OPENCL
```

이부분만 수정한다 

<img width="903" alt="image" src="https://user-images.githubusercontent.com/60145951/175660864-4874bb12-4eb4-40eb-a13c-3c0702e0ca17.png">

이상태로 configure 하면 빨간색이 사라진다

<img width="1036" alt="image" src="https://user-images.githubusercontent.com/60145951/175665680-4a73198d-09dd-4508-9c56-efc4839161f8.png">


generate 하여 단계를 완료한다.

<img width="869" alt="image" src="https://user-images.githubusercontent.com/60145951/175661266-09ed3ba3-6256-420b-832a-25fd95c086fd.png">

terminal 로 돌아와 <루트경로>/build 에서 MacOS용 make 명령어를 입력한다

```bash
# https://cmu-perceptual-computing-lab.github.io/openpose/web/html/doc/md_doc_installation_0_index.html#compilation
make -j`sysctl -n hw.logicalcpu`
```


<br>

#### **[issue 1]** cblas.h not found : [try 5] openblas 경로 찾아 CMakeList.txt 변경하는것으로 해결

<details>
<summary> issue_no1.log </summary>

<img width="1065" alt="image" src="https://user-images.githubusercontent.com/60145951/175806665-c7312d4a-fd56-4fce-997e-478ac5120700.png">


##### try 1 : `Makefile.config` 수정
<img width="937" alt="image" src="https://user-images.githubusercontent.com/60145951/175665183-a7a7b60d-9e8f-434a-a207-09460b89cd6d.png">

- https://stackoverflow.com/questions/40668588/cblas-h-error-while-installing-caffe-on-mac-os
- https://github.com/BVLC/caffe/issues/3599
- https://letni.tistory.com/18

등에서 `open-blas` 안에 `cblas.h`가 포함되어 있으니 이를 설치하라고 조언하는데 `brew install openblas` 을 해도 같은 문제가 발생한다. conda와 충돌이 있다는 말도 있어 꺼봤지만 달라지지는 않았다. 

brew --prefix 로 확인한 위치에 cblas가 있는데 불러오지 못하는 것 같다.

<img width="725" alt="image" src="https://user-images.githubusercontent.com/60145951/175668020-453e793a-2e71-45ae-86b7-738d77c24ad0.png">

[여기](https://github.com/amd/OpenCL-caffe/issues/36#issuecomment-199587164)서 말하는 바에 따르면 brew로 설치한 경우 주석 처리를 제거해야 한다는데 `Makefile.config`를 어디서 찾을지 모르겠다. == [build dir의 최상위에 있다고 한다.](https://releases.llvm.org/1.6/docs/MakefileGuide.html#:~:text=Makefile.config-,Every%20project%20must%20have%20a%20Makefile.,of%20the%20project's%20source%20directory.) .config 가 없는 Makefile을 말하는 것 같아 편집하기로 했다. 그러나 변화는 없었음.

##### try 2 : Xcode update, Mac에서는 C compile을 XCode에서 한다.

| 추정 원인 | reference | 주요 시도방법 | 결과 |
| :-------  | :-------  | :------------ | :--- |
| MacOS에서의 Clang compile | 어느 스택오버플로에서 봤는데 링크 기록 X | Xcode 설치/업데이트, command line tool 설치 | build 중단 퍼센테이지만 달라지지 여전함 |

1. XCode는 기본적으로 설치되어 나온다고 하는데 살펴보니 설치되지 않았음. Apple 개발자 센터에서 수동으로 설치
    - 10GB쯤 되는 압축파일을 받아서 해제만 하면 이후는 자동으로 된다.
    - App으로 옮기기만 하면 OK
2. command line tool: terminal에서 `xcode-select --install` 명령어로 설치
    - 설치되어 있었는데 뭐가 문제일까... 사실 설치되어 있을 수 밖에 없는게 이게 없으면 CLI 환경에서 작업이 안됨

    ```bash
    (base)    ~  xcode-select --install
    xcode-select: error: command line tools are already installed, use "Software Update" to install updates
    (base)  ✘   ~  xcode-select --version
    xcode-select version 2395.
    ```
3. CMake_OSX_SYSROOT 에 적힌  `/Library/Developer/CommandLineTools/SDKs/MacOSX12.3.sdk` 가 제대로 된 파일인지도 확인함

<img width="600" alt="image" src="https://user-images.githubusercontent.com/60145951/175805407-35843560-4718-4180-a8eb-42a10257bcaa.png">

<details>

<summary> error log </summary>

```bash
[  0%] Creating directories for 'openpose_lib'
[  1%] No download step for 'openpose_lib'
[  1%] No update step for 'openpose_lib'
[  2%] No patch step for 'openpose_lib'
Consolidate compiler generated dependencies of target openpose
[  2%] Performing configure step for 'openpose_lib'
CMake Deprecation Warning at CMakeLists.txt:1 (cmake_minimum_required):
  Compatibility with CMake < 2.8.12 will be removed from a future version of
  CMake.

  Update the VERSION argument <min> value or use a ...<max> suffix to tell
  CMake that the project does not need compatibility with older versions.


-- The C compiler identification is AppleClang 13.1.6.13160021
-- The CXX compiler identification is AppleClang 13.1.6.13160021
-- Detecting C compiler ABI info
[  2%] Building CXX object src/openpose/CMakeFiles/openpose.dir/3d/cameraParameterReader.cpp.o
[  3%] Building CXX object src/openpose/CMakeFiles/openpose.dir/3d/jointAngleEstimation.cpp.o
[  3%] Building CXX object src/openpose/CMakeFiles/openpose.dir/3d/defineTemplates.cpp.o
[  4%] Building CXX object src/openpose/CMakeFiles/openpose.dir/calibration/cameraParameterEstimation.cpp.o
[  4%] Building CXX object src/openpose/CMakeFiles/openpose.dir/3d/poseTriangulation.cpp.o
[  4%] Building CXX object src/openpose/CMakeFiles/openpose.dir/3d/poseTriangulationPrivate.cpp.o
[  4%] Building CXX object src/openpose/CMakeFiles/openpose.dir/calibration/gridPatternFunctions.cpp.o
[  5%] Building CXX object src/openpose/CMakeFiles/openpose.dir/core/array.cpp.o
-- Detecting C compiler ABI info - done
-- Check for working C compiler: /Library/Developer/CommandLineTools/usr/bin/cc - skipped
-- Detecting C compile features
-- Detecting C compile features - done
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Check for working CXX compiler: /Library/Developer/CommandLineTools/usr/bin/c++ - skipped
-- Detecting CXX compile features
-- Detecting CXX compile features - done
CMake Warning (dev) at cmake/Misc.cmake:32 (set):
  implicitly converting 'BOOLEAN' to 'STRING' type.
Call Stack (most recent call first):
  CMakeLists.txt:25 (include)
This warning is for project developers.  Use -Wno-dev to suppress it.

[  5%] Building CXX object src/openpose/CMakeFiles/openpose.dir/core/arrayCpuGpu.cpp.o
[  5%] Building CXX object src/openpose/CMakeFiles/openpose.dir/core/cvMatToOpInput.cpp.o
-- Found Boost: /opt/homebrew/lib/cmake/Boost-1.78.0/BoostConfig.cmake (found suitable version "1.78.0", minimum required is "1.54") found components: system thread filesystem
-- Found Threads: TRUE
[  6%] Building CXX object src/openpose/CMakeFiles/openpose.dir/core/cvMatToOpOutput.cpp.o
-- Found GFlags: /opt/homebrew/include
-- Found gflags  (include: /opt/homebrew/include, library: /opt/homebrew/lib/libgflags.dylib)
-- Found Glog: /opt/homebrew/include
-- Found glog    (include: /opt/homebrew/include, library: /opt/homebrew/lib/libglog.dylib)
-- Found Protobuf: /opt/homebrew/lib/libprotobuf.dylib (found version "3.19.4")
-- Found PROTOBUF Compiler: /opt/homebrew/bin/protoc
[  6%] Building CXX object src/openpose/CMakeFiles/openpose.dir/core/datum.cpp.o
[  6%] Building CXX object src/openpose/CMakeFiles/openpose.dir/core/defineTemplates.cpp.o
[  7%] Building CXX object src/openpose/CMakeFiles/openpose.dir/core/gpuRenderer.cpp.o
[  7%] Building CXX object src/openpose/CMakeFiles/openpose.dir/core/keepTopNPeople.cpp.o
[  8%] Building CXX object src/openpose/CMakeFiles/openpose.dir/core/keypointScaler.cpp.o
[  8%] Building CXX object src/openpose/CMakeFiles/openpose.dir/core/matrix.cpp.o
[  8%] Building CXX object src/openpose/CMakeFiles/openpose.dir/core/opOutputToCvMat.cpp.o
[  9%] Building CXX object src/openpose/CMakeFiles/openpose.dir/core/point.cpp.o
[  9%] Building CXX object src/openpose/CMakeFiles/openpose.dir/core/rectangle.cpp.o
[  9%] Building CXX object src/openpose/CMakeFiles/openpose.dir/core/renderer.cpp.o
-- Found HDF5: /opt/homebrew/Cellar/hdf5/1.12.2/lib/libhdf5.dylib;/opt/homebrew/opt/libaec/lib/libsz.dylib;/Library/Developer/CommandLineTools/SDKs/MacOSX12.3.sdk/usr/lib/libz.tbd;/Library/Developer/CommandLineTools/SDKs/MacOSX12.3.sdk/usr/lib/libdl.tbd;/Library/Developer/CommandLineTools/SDKs/MacOSX12.3.sdk/usr/lib/libm.tbd;/opt/homebrew/Cellar/hdf5/1.12.2/lib/libhdf5_cpp.dylib;/opt/homebrew/Cellar/hdf5/1.12.2/lib/libhdf5.dylib;/opt/homebrew/opt/libaec/lib/libsz.dylib;/Library/Developer/CommandLineTools/SDKs/MacOSX12.3.sdk/usr/lib/libz.tbd;/Library/Developer/CommandLineTools/SDKs/MacOSX12.3.sdk/usr/lib/libdl.tbd;/Library/Developer/CommandLineTools/SDKs/MacOSX12.3.sdk/usr/lib/libm.tbd (found version "1.12.2") found components: HL
[ 10%] Building CXX object src/openpose/CMakeFiles/openpose.dir/core/scaleAndSizeExtractor.cpp.o
[ 10%] Building CXX object src/openpose/CMakeFiles/openpose.dir/core/string.cpp.o
[ 11%] Building CXX object src/openpose/CMakeFiles/openpose.dir/core/verbosePrinter.cpp.o
[ 11%] Building CXX object src/openpose/CMakeFiles/openpose.dir/face/defineTemplates.cpp.o
[ 11%] Building CXX object src/openpose/CMakeFiles/openpose.dir/face/faceDetector.cpp.o
[ 12%] Building CXX object src/openpose/CMakeFiles/openpose.dir/face/faceDetectorOpenCV.cpp.o
[ 12%] Building CXX object src/openpose/CMakeFiles/openpose.dir/face/faceExtractorCaffe.cpp.o
[ 13%] Building CXX object src/openpose/CMakeFiles/openpose.dir/face/faceExtractorNet.cpp.o
[ 13%] Building CXX object src/openpose/CMakeFiles/openpose.dir/face/faceCpuRenderer.cpp.o
[ 13%] Building CXX object src/openpose/CMakeFiles/openpose.dir/face/faceGpuRenderer.cpp.o
[ 14%] Building CXX object src/openpose/CMakeFiles/openpose.dir/face/faceRenderer.cpp.o
[ 14%] Building CXX object src/openpose/CMakeFiles/openpose.dir/face/renderFace.cpp.o
-- -- CUDA is disabled. Building without it...
CMake Error at /Applications/CMake.app/Contents/share/cmake-3.23/Modules/FindPackageHandleStandardArgs.cmake:230 (message):
  Could NOT find vecLib (missing: vecLib_INCLUDE_DIR)
Call Stack (most recent call first):
  /Applications/CMake.app/Contents/share/cmake-3.23/Modules/FindPackageHandleStandardArgs.cmake:594 (_FPHSA_FAILURE_MESSAGE)
  cmake/Modules/FindvecLib.cmake:24 (find_package_handle_standard_args)
  cmake/Dependencies.cmake:135 (find_package)
  CMakeLists.txt:49 (include)


-- Configuring incomplete, errors occurred!
See also "/Users/a4923/Desktop/repositories/_construct/openpose/build/caffe/src/openpose_lib-build/CMakeFiles/CMakeOutput.log".
See also "/Users/a4923/Desktop/repositories/_construct/openpose/build/caffe/src/openpose_lib-build/CMakeFiles/CMakeError.log".
make[2]: *** [caffe/src/openpose_lib-stamp/openpose_lib-configure] Error 1
make[1]: *** [CMakeFiles/openpose_lib.dir/all] Error 2
make[1]: *** Waiting for unfinished jobs....
[ 14%] Building CXX object src/openpose/CMakeFiles/openpose.dir/filestream/bvhSaver.cpp.o
[ 15%] Building CXX object src/openpose/CMakeFiles/openpose.dir/filestream/cocoJsonSaver.cpp.o
[ 15%] Building CXX object src/openpose/CMakeFiles/openpose.dir/filestream/defineTemplates.cpp.o
[ 16%] Building CXX object src/openpose/CMakeFiles/openpose.dir/filestream/fileSaver.cpp.o
[ 16%] Building CXX object src/openpose/CMakeFiles/openpose.dir/filestream/fileStream.cpp.o
[ 16%] Building CXX object src/openpose/CMakeFiles/openpose.dir/filestream/heatMapSaver.cpp.o
[ 17%] Building CXX object src/openpose/CMakeFiles/openpose.dir/filestream/imageSaver.cpp.o
[ 17%] Building CXX object src/openpose/CMakeFiles/openpose.dir/filestream/jsonOfstream.cpp.o
[ 17%] Building CXX object src/openpose/CMakeFiles/openpose.dir/filestream/keypointSaver.cpp.o
[ 18%] Building CXX object src/openpose/CMakeFiles/openpose.dir/filestream/peopleJsonSaver.cpp.o
[ 18%] Building CXX object src/openpose/CMakeFiles/openpose.dir/filestream/udpSender.cpp.o
[ 19%] Building CXX object src/openpose/CMakeFiles/openpose.dir/filestream/videoSaver.cpp.o
[ 19%] Building CXX object src/openpose/CMakeFiles/openpose.dir/gpu/cuda.cpp.o
[ 19%] Building CXX object src/openpose/CMakeFiles/openpose.dir/gpu/gpu.cpp.o
[ 20%] Building CXX object src/openpose/CMakeFiles/openpose.dir/gpu/opencl.cpp.o
[ 20%] Building CXX object src/openpose/CMakeFiles/openpose.dir/gui/defineTemplates.cpp.o
[ 21%] Building CXX object src/openpose/CMakeFiles/openpose.dir/gui/frameDisplayer.cpp.o
[ 21%] Building CXX object src/openpose/CMakeFiles/openpose.dir/gui/gui.cpp.o
[ 21%] Building CXX object src/openpose/CMakeFiles/openpose.dir/gui/guiAdam.cpp.o
[ 22%] Building CXX object src/openpose/CMakeFiles/openpose.dir/gui/gui3D.cpp.o
[ 22%] Building CXX object src/openpose/CMakeFiles/openpose.dir/gui/guiInfoAdder.cpp.o
[ 22%] Building CXX object src/openpose/CMakeFiles/openpose.dir/hand/defineTemplates.cpp.o
[ 23%] Building CXX object src/openpose/CMakeFiles/openpose.dir/hand/handDetector.cpp.o
[ 23%] Building CXX object src/openpose/CMakeFiles/openpose.dir/hand/handDetectorFromTxt.cpp.o
[ 24%] Building CXX object src/openpose/CMakeFiles/openpose.dir/hand/handExtractorCaffe.cpp.o
[ 24%] Building CXX object src/openpose/CMakeFiles/openpose.dir/hand/handExtractorNet.cpp.o
[ 24%] Building CXX object src/openpose/CMakeFiles/openpose.dir/hand/handCpuRenderer.cpp.o
[ 25%] Building CXX object src/openpose/CMakeFiles/openpose.dir/hand/handGpuRenderer.cpp.o
[ 25%] Building CXX object src/openpose/CMakeFiles/openpose.dir/hand/handRenderer.cpp.o
[ 25%] Building CXX object src/openpose/CMakeFiles/openpose.dir/hand/renderHand.cpp.o
[ 26%] Building CXX object src/openpose/CMakeFiles/openpose.dir/net/bodyPartConnectorBase.cpp.o
[ 26%] Building CXX object src/openpose/CMakeFiles/openpose.dir/net/bodyPartConnectorBaseCL.cpp.o
[ 27%] Building CXX object src/openpose/CMakeFiles/openpose.dir/net/bodyPartConnectorCaffe.cpp.o
[ 27%] Building CXX object src/openpose/CMakeFiles/openpose.dir/net/maximumBase.cpp.o
[ 27%] Building CXX object src/openpose/CMakeFiles/openpose.dir/net/maximumCaffe.cpp.o
[ 28%] Building CXX object src/openpose/CMakeFiles/openpose.dir/net/netCaffe.cpp.o
[ 28%] Building CXX object src/openpose/CMakeFiles/openpose.dir/net/netOpenCv.cpp.o
[ 28%] Building CXX object src/openpose/CMakeFiles/openpose.dir/net/nmsBase.cpp.o
[ 29%] Building CXX object src/openpose/CMakeFiles/openpose.dir/net/nmsBaseCL.cpp.o
In file included from /Users/a4923/Desktop/repositories/_construct/openpose/src/openpose/net/netCaffe.cpp:6:
In file included from /opt/homebrew/include/caffe/net.hpp:12:
In file included from /opt/homebrew/include/caffe/layer.hpp:12:
In file included from /opt/homebrew/include/caffe/util/math_functions.hpp:11:
/opt/homebrew/include/caffe/util/mkl_alternate.hpp:14:10: fatal error: 'cblas.h' file not found
#include <cblas.h>
         ^~~~~~~~~
1 error generated.
make[2]: *** [src/openpose/CMakeFiles/openpose.dir/net/netCaffe.cpp.o] Error 1
make[2]: *** Waiting for unfinished jobs....
In file included from /Users/a4923/Desktop/repositories/_construct/openpose/src/openpose/net/netOpenCv.cpp:7:
In file included from /opt/homebrew/include/caffe/net.hpp:12:
In file included from /opt/homebrew/include/caffe/layer.hpp:12:
In file included from /opt/homebrew/include/caffe/util/math_functions.hpp:11:
/opt/homebrew/include/caffe/util/mkl_alternate.hpp:14:10: fatal error: 'cblas.h' file not found
#include <cblas.h>
         ^~~~~~~~~
1 error generated.
make[2]: *** [src/openpose/CMakeFiles/openpose.dir/net/netOpenCv.cpp.o] Error 1
make[1]: *** [src/openpose/CMakeFiles/openpose.dir/all] Error 2
make: *** [all] Error 2
(base)  ✘   ~/Desktop/repositories/_construct/openpose/build   master ±  where cblas
cblas not found
(base)  ✘   ~/Desktop/repositories/_construct/openpose/build   master ±  where cblas.h
cblas.h not found
(base)  ✘   ~/Desktop/repositories/_construct/openpose/build   master ±  where blas
blas not found
(base)  ✘   ~/Desktop/repositories/_construct/openpose/build   master ±  pip install cblas
ERROR: Could not find a version that satisfies the requirement cblas (from versions: none)
ERROR: No matching distribution found for cblas
WARNING: You are using pip version 22.0.4; however, version 22.1.2 is available.
You should consider upgrading via the '/Users/a4923/.pyenv/versions/3.8.13/bin/python3.8 -m pip install --upgrade pip' command.
(base)  ✘   ~/Desktop/repositories/_construct/openpose/build   master ±  make -j`sysctl -n hw.logicalcpu`
-- GCC detected, adding compile flags
-- Building CPU Only.
-- Found gflags  (include: /opt/homebrew/include, library: /opt/homebrew/lib/libgflags.dylib)
-- Found glog    (include: /opt/homebrew/include, library: /opt/homebrew/lib/libglog.dylib)
-- Could NOT find OpenMP_C (missing: OpenMP_C_FLAGS OpenMP_C_LIB_NAMES)
-- Could NOT find OpenMP_CXX (missing: OpenMP_CXX_FLAGS OpenMP_CXX_LIB_NAMES)
-- Could NOT find OpenMP (missing: OpenMP_C_FOUND OpenMP_CXX_FOUND)
-- Caffe will be downloaded from source now. NOTE: This process might take several minutes depending
        on your internet connection.
-- Caffe has already been downloaded.
HEAD is now at 1807aada Added Ampere arch's (CUDA11)
-- Caffe will be built from source now.
-- Download the models.
-- Downloading BODY_25 model...
-- Model already exists.
-- Not downloading body (COCO) model
-- Not downloading body (MPI) model
-- Downloading face model...
-- Model already exists.
-- Downloading hand model...
-- Model already exists.
-- Models Downloaded.
-- Configuring done
-- Generating done
-- Build files have been written to: /Users/a4923/Desktop/repositories/_construct/openpose/build
[  0%] Performing configure step for 'openpose_lib'
CMake Deprecation Warning at CMakeLists.txt:1 (cmake_minimum_required):
  Compatibility with CMake < 2.8.12 will be removed from a future version of
  CMake.

  Update the VERSION argument <min> value or use a ...<max> suffix to tell
  CMake that the project does not need compatibility with older versions.


CMake Warning (dev) at cmake/Misc.cmake:32 (set):
  implicitly converting 'BOOLEAN' to 'STRING' type.
Call Stack (most recent call first):
  CMakeLists.txt:25 (include)
This warning is for project developers.  Use -Wno-dev to suppress it.

Consolidate compiler generated dependencies of target openpose
-- Found gflags  (include: /opt/homebrew/include, library: /opt/homebrew/lib/libgflags.dylib)
-- Found glog    (include: /opt/homebrew/include, library: /opt/homebrew/lib/libglog.dylib)
-- Found PROTOBUF Compiler: /opt/homebrew/bin/protoc
[  0%] Building CXX object src/openpose/CMakeFiles/openpose.dir/3d/cameraParameterReader.cpp.o
[  0%] Building CXX object src/openpose/CMakeFiles/openpose.dir/3d/poseTriangulation.cpp.o
[  0%] Building CXX object src/openpose/CMakeFiles/openpose.dir/3d/defineTemplates.cpp.o
[  0%] Building CXX object src/openpose/CMakeFiles/openpose.dir/calibration/gridPatternFunctions.cpp.o
[  1%] Building CXX object src/openpose/CMakeFiles/openpose.dir/3d/jointAngleEstimation.cpp.o
[  1%] Building CXX object src/openpose/CMakeFiles/openpose.dir/3d/poseTriangulationPrivate.cpp.o
[  2%] Building CXX object src/openpose/CMakeFiles/openpose.dir/calibration/cameraParameterEstimation.cpp.o
[  3%] Building CXX object src/openpose/CMakeFiles/openpose.dir/core/array.cpp.o
[  3%] Building CXX object src/openpose/CMakeFiles/openpose.dir/core/arrayCpuGpu.cpp.o
[  3%] Building CXX object src/openpose/CMakeFiles/openpose.dir/core/cvMatToOpInput.cpp.o
[  4%] Building CXX object src/openpose/CMakeFiles/openpose.dir/core/cvMatToOpOutput.cpp.o
[  4%] Building CXX object src/openpose/CMakeFiles/openpose.dir/core/datum.cpp.o
[  4%] Building CXX object src/openpose/CMakeFiles/openpose.dir/core/defineTemplates.cpp.o
[  5%] Building CXX object src/openpose/CMakeFiles/openpose.dir/core/gpuRenderer.cpp.o
[  5%] Building CXX object src/openpose/CMakeFiles/openpose.dir/core/keepTopNPeople.cpp.o
[  6%] Building CXX object src/openpose/CMakeFiles/openpose.dir/core/keypointScaler.cpp.o
-- -- CUDA is disabled. Building without it...
CMake Error at /Applications/CMake.app/Contents/share/cmake-3.23/Modules/FindPackageHandleStandardArgs.cmake:230 (message):
  Could NOT find vecLib (missing: vecLib_INCLUDE_DIR)
Call Stack (most recent call first):
  /Applications/CMake.app/Contents/share/cmake-3.23/Modules/FindPackageHandleStandardArgs.cmake:594 (_FPHSA_FAILURE_MESSAGE)
  cmake/Modules/FindvecLib.cmake:24 (find_package_handle_standard_args)
  cmake/Dependencies.cmake:135 (find_package)
  CMakeLists.txt:49 (include)


-- Configuring incomplete, errors occurred!
See also "/Users/a4923/Desktop/repositories/_construct/openpose/build/caffe/src/openpose_lib-build/CMakeFiles/CMakeOutput.log".
See also "/Users/a4923/Desktop/repositories/_construct/openpose/build/caffe/src/openpose_lib-build/CMakeFiles/CMakeError.log".
[  6%] Building CXX object src/openpose/CMakeFiles/openpose.dir/core/matrix.cpp.o
make[2]: *** [caffe/src/openpose_lib-stamp/openpose_lib-configure] Error 1
make[1]: *** [CMakeFiles/openpose_lib.dir/all] Error 2
make[1]: *** Waiting for unfinished jobs....
[  6%] Building CXX object src/openpose/CMakeFiles/openpose.dir/core/opOutputToCvMat.cpp.o
[  7%] Building CXX object src/openpose/CMakeFiles/openpose.dir/core/point.cpp.o
[  7%] Building CXX object src/openpose/CMakeFiles/openpose.dir/core/rectangle.cpp.o
[  7%] Building CXX object src/openpose/CMakeFiles/openpose.dir/core/renderer.cpp.o
[  8%] Building CXX object src/openpose/CMakeFiles/openpose.dir/core/scaleAndSizeExtractor.cpp.o
[  9%] Building CXX object src/openpose/CMakeFiles/openpose.dir/core/string.cpp.o
[  9%] Building CXX object src/openpose/CMakeFiles/openpose.dir/core/verbosePrinter.cpp.o
[  9%] Building CXX object src/openpose/CMakeFiles/openpose.dir/face/defineTemplates.cpp.o
[  9%] Building CXX object src/openpose/CMakeFiles/openpose.dir/face/faceDetector.cpp.o
[ 10%] Building CXX object src/openpose/CMakeFiles/openpose.dir/face/faceDetectorOpenCV.cpp.o
[ 10%] Building CXX object src/openpose/CMakeFiles/openpose.dir/face/faceExtractorCaffe.cpp.o
[ 11%] Building CXX object src/openpose/CMakeFiles/openpose.dir/face/faceExtractorNet.cpp.o
[ 11%] Building CXX object src/openpose/CMakeFiles/openpose.dir/face/faceCpuRenderer.cpp.o
[ 11%] Building CXX object src/openpose/CMakeFiles/openpose.dir/face/faceGpuRenderer.cpp.o
[ 12%] Building CXX object src/openpose/CMakeFiles/openpose.dir/face/faceRenderer.cpp.o
[ 12%] Building CXX object src/openpose/CMakeFiles/openpose.dir/face/renderFace.cpp.o
[ 12%] Building CXX object src/openpose/CMakeFiles/openpose.dir/filestream/bvhSaver.cpp.o
[ 13%] Building CXX object src/openpose/CMakeFiles/openpose.dir/filestream/cocoJsonSaver.cpp.o
[ 13%] Building CXX object src/openpose/CMakeFiles/openpose.dir/filestream/defineTemplates.cpp.o
[ 14%] Building CXX object src/openpose/CMakeFiles/openpose.dir/filestream/fileSaver.cpp.o
[ 14%] Building CXX object src/openpose/CMakeFiles/openpose.dir/filestream/fileStream.cpp.o
[ 14%] Building CXX object src/openpose/CMakeFiles/openpose.dir/filestream/heatMapSaver.cpp.o
[ 15%] Building CXX object src/openpose/CMakeFiles/openpose.dir/filestream/imageSaver.cpp.o
[ 15%] Building CXX object src/openpose/CMakeFiles/openpose.dir/filestream/jsonOfstream.cpp.o
[ 15%] Building CXX object src/openpose/CMakeFiles/openpose.dir/filestream/keypointSaver.cpp.o
[ 16%] Building CXX object src/openpose/CMakeFiles/openpose.dir/filestream/peopleJsonSaver.cpp.o
[ 16%] Building CXX object src/openpose/CMakeFiles/openpose.dir/filestream/udpSender.cpp.o
[ 17%] Building CXX object src/openpose/CMakeFiles/openpose.dir/filestream/videoSaver.cpp.o
[ 17%] Building CXX object src/openpose/CMakeFiles/openpose.dir/gpu/cuda.cpp.o
[ 17%] Building CXX object src/openpose/CMakeFiles/openpose.dir/gpu/gpu.cpp.o
[ 18%] Building CXX object src/openpose/CMakeFiles/openpose.dir/gpu/opencl.cpp.o
[ 18%] Building CXX object src/openpose/CMakeFiles/openpose.dir/gui/defineTemplates.cpp.o
[ 19%] Building CXX object src/openpose/CMakeFiles/openpose.dir/gui/frameDisplayer.cpp.o
[ 19%] Building CXX object src/openpose/CMakeFiles/openpose.dir/gui/gui.cpp.o
[ 19%] Building CXX object src/openpose/CMakeFiles/openpose.dir/gui/guiAdam.cpp.o
[ 20%] Building CXX object src/openpose/CMakeFiles/openpose.dir/gui/gui3D.cpp.o
[ 20%] Building CXX object src/openpose/CMakeFiles/openpose.dir/gui/guiInfoAdder.cpp.o
[ 20%] Building CXX object src/openpose/CMakeFiles/openpose.dir/hand/defineTemplates.cpp.o
[ 21%] Building CXX object src/openpose/CMakeFiles/openpose.dir/hand/handDetector.cpp.o
[ 21%] Building CXX object src/openpose/CMakeFiles/openpose.dir/hand/handDetectorFromTxt.cpp.o
[ 22%] Building CXX object src/openpose/CMakeFiles/openpose.dir/hand/handExtractorCaffe.cpp.o
[ 22%] Building CXX object src/openpose/CMakeFiles/openpose.dir/hand/handExtractorNet.cpp.o
[ 22%] Building CXX object src/openpose/CMakeFiles/openpose.dir/hand/handCpuRenderer.cpp.o
[ 23%] Building CXX object src/openpose/CMakeFiles/openpose.dir/hand/handGpuRenderer.cpp.o
[ 23%] Building CXX object src/openpose/CMakeFiles/openpose.dir/hand/handRenderer.cpp.o
[ 23%] Building CXX object src/openpose/CMakeFiles/openpose.dir/hand/renderHand.cpp.o
[ 24%] Building CXX object src/openpose/CMakeFiles/openpose.dir/net/bodyPartConnectorBase.cpp.o
[ 24%] Building CXX object src/openpose/CMakeFiles/openpose.dir/net/bodyPartConnectorBaseCL.cpp.o
[ 25%] Building CXX object src/openpose/CMakeFiles/openpose.dir/net/bodyPartConnectorCaffe.cpp.o
[ 25%] Building CXX object src/openpose/CMakeFiles/openpose.dir/net/maximumBase.cpp.o
[ 25%] Building CXX object src/openpose/CMakeFiles/openpose.dir/net/maximumCaffe.cpp.o
[ 26%] Building CXX object src/openpose/CMakeFiles/openpose.dir/net/netCaffe.cpp.o
[ 26%] Building CXX object src/openpose/CMakeFiles/openpose.dir/net/netOpenCv.cpp.o
[ 26%] Building CXX object src/openpose/CMakeFiles/openpose.dir/net/nmsBase.cpp.o
[ 27%] Building CXX object src/openpose/CMakeFiles/openpose.dir/net/nmsBaseCL.cpp.o
In file included from /Users/a4923/Desktop/repositories/_construct/openpose/src/openpose/net/netCaffe.cpp:6:
In file included from /opt/homebrew/include/caffe/net.hpp:12:
In file included from /opt/homebrew/include/caffe/layer.hpp:12:
In file included from /opt/homebrew/include/caffe/util/math_functions.hpp:11:
/opt/homebrew/include/caffe/util/mkl_alternate.hpp:14:10: fatal error: 'cblas.h' file not found
#include <cblas.h>
         ^~~~~~~~~
In file included from /Users/a4923/Desktop/repositories/_construct/openpose/src/openpose/net/netOpenCv.cpp:7:
In file included from /opt/homebrew/include/caffe/net.hpp:12:
In file included from /opt/homebrew/include/caffe/layer.hpp:12:
In file included from /opt/homebrew/include/caffe/util/math_functions.hpp:11:
/opt/homebrew/include/caffe/util/mkl_alternate.hpp:14:10: fatal error: 'cblas.h' file not found
#include <cblas.h>
         ^~~~~~~~~
1 error generated.
make[2]: *** [src/openpose/CMakeFiles/openpose.dir/net/netCaffe.cpp.o] Error 1
make[2]: *** Waiting for unfinished jobs....
1 error generated.
make[2]: *** [src/openpose/CMakeFiles/openpose.dir/net/netOpenCv.cpp.o] Error 1
make[1]: *** [src/openpose/CMakeFiles/openpose.dir/all] Error 2
make: *** [all] Error 2
```

</details>

##### try 3 : cmake; caffe path

| 추정 원인 | reference | 주요 시도방법 | 결과 |
| :-------  | :-------  | :------------ | :--- |
| Caffe 위치가 잘못된게 아닌가 | [공식문서](https://cmu-perceptual-computing-lab.github.io/openpose/web/html/doc/md_doc_installation_0_index.html#:~:text=Windows%20Portable%20Demo,-If%20you%20just&text=Download%20the%20latest%20OpenPose%20version,following%20doc%2F01_demo.md.)의 위치와 다름, | reinstall, 경로에 따라 설치위치 찾아봄 | 헛발질인듯, 경로 /opt/homebrew/include/caffe/ 로 원상복귀 |

```
If the default compilation fails with Caffe errors, install Caffe separately and set BUILD_CAFFE to false in the CMake config. Steps:

Re-create the build folder: rm -rf build; mkdir build; cd build.
brew uninstall caffe to remove the version of Caffe previously installed via cmake.
brew install caffe to install Caffe separately.
Run cmake-gui and make the following adjustments to the cmake config:
    1. BUILD_CAFFE set to false.
    2. Caffe_INCLUDE_DIRS set to /usr/local/include/caffe.
    3. Caffe_LIBS set to /usr/local/lib/libcaffe.dylib.
    4. Run Configure and Generate from CMake GUI.
If you face an OpenCV error during compiling time similar to ‘fatal error: 'opencv2/highgui/highgui.hpp’ file not found`, please apply the following patch (this error has been reported in the latest OSX 10.14):
```

Caffe를 configure 과정에서 build한다는 것 같으니 미리 설치해놓고 거기서 불러오라는 이야기 같다. 다만 위치가 걱정인데 /usr/local/include에는 caffe가 없었다. (brew로 설치했기 때문) 에러 메시지에서 caffe를 불러오는 위치를 보면 `/opt/homebrew/include/caffe/` 위치가 맞으므로 경로를 해당 경로로 변경한다.
- 우려되는 지점은: lib/libcaffe.dylib 을 못찾겠다는 점.
- grep 으로 아무리 찾아도 나오질 않는다.

> 주요 에러메시지
> 

##### try 4 : Mac에 c compile 환경 구축

- [caffe 공식문서](https://caffe.berkeleyvision.org/install_osx.html) 에 보니 CUDA 7을 사용하라고 하지만 M1은 해당 없음
- 그 외 gcc나 설치해야할 게 있는지 확인해봤으나 여전히 해당사항 없음. 
- error log에서 cpp를 제대로 읽지만 include만 못하는 것으로 보아 컴파일 자체가 안되는 상황은 아닌 듯

##### try 4 : conda로 blas 재설치

Blas가 이미 존재하는것으로 확인되었지만 불러오지 못한다면 다시 깔아봤는데 conda로 관리되는게 아닌지 실패

```bash
conda install -c conda-forge blas
```

##### try 5 : 결국 cblas를 못불러오는게 문제이니 CMakeList.txt 에서 경로 수정

[CMakeList.txt](https://bytemeta.vip/repo/CMU-Perceptual-Computing-Lab/openpose/issues/1942)를 변경하라고 해서 했는데... 에러 로그에 변경된 위치를 찾는것은 발견되지만 여전히 해결되지 않아서 해당 위치로 가 파일이 존재하는지 확인, 해당 위치에 openblas가 없는 것을 파악함. 따라서 openblas 위치를 찾아 CMakeList를 변경하니 해당 내용 해결됨

<details>

<summary> error log </summary>

```bash
[  0%] Performing configure step for 'openpose_lib'
CMake Deprecation Warning at CMakeLists.txt:1 (cmake_minimum_required):
  Compatibility with CMake < 2.8.12 will be removed from a future version of
  CMake.

  Update the VERSION argument <min> value or use a ...<max> suffix to tell
  CMake that the project does not need compatibility with older versions.


CMake Warning (dev) at cmake/Misc.cmake:32 (set):
  implicitly converting 'BOOLEAN' to 'STRING' type.
Call Stack (most recent call first):
  CMakeLists.txt:25 (include)
This warning is for project developers.  Use -Wno-dev to suppress it.

Consolidate compiler generated dependencies of target openpose
-- Found gflags  (include: /opt/homebrew/include, library: /opt/homebrew/lib/libgflags.dylib)
-- Found glog    (include: /opt/homebrew/include, library: /opt/homebrew/lib/libglog.dylib)
-- Found PROTOBUF Compiler: /opt/homebrew/bin/protoc
[  1%] Building CXX object src/openpose/CMakeFiles/openpose.dir/net/netOpenCv.cpp.o
[  1%] Building CXX object src/openpose/CMakeFiles/openpose.dir/net/netCaffe.cpp.o
[  1%] Building CXX object src/openpose/CMakeFiles/openpose.dir/net/nmsCaffe.cpp.o
[  2%] Building CXX object src/openpose/CMakeFiles/openpose.dir/pose/defineTemplates.cpp.o
[  2%] Building CXX object src/openpose/CMakeFiles/openpose.dir/net/resizeAndMergeBaseCL.cpp.o
[  2%] Building CXX object src/openpose/CMakeFiles/openpose.dir/net/resizeAndMergeCaffe.cpp.o
[  3%] Building CXX object src/openpose/CMakeFiles/openpose.dir/net/resizeAndMergeBase.cpp.o
[  3%] Building CXX object src/openpose/CMakeFiles/openpose.dir/pose/poseCpuRenderer.cpp.o
[  4%] Building CXX object src/openpose/CMakeFiles/openpose.dir/pose/poseExtractor.cpp.o
In file included from /Users/a4923/Desktop/repositories/_construct/openpose/src/openpose/net/netCaffe.cpp:6:
In file included from /opt/homebrew/include/caffe/net.hpp:12:
In file included from /opt/homebrew/include/caffe/layer.hpp:12:
In file included from /opt/homebrew/include/caffe/util/math_functions.hpp:11:
/opt/homebrew/include/caffe/util/mkl_alternate.hpp:14:10: fatal error: 'cblas.h' file not found
#include <cblas.h>
         ^~~~~~~~~
In file included from /Users/a4923/Desktop/repositories/_construct/openpose/src/openpose/net/netOpenCv.cpp:7:
In file included from /opt/homebrew/include/caffe/net.hpp:12:
In file included from /opt/homebrew/include/caffe/layer.hpp:12:
In file included from /opt/homebrew/include/caffe/util/math_functions.hpp:11:
/opt/homebrew/include/caffe/util/mkl_alternate.hpp:14:10: fatal error: 'cblas.h' file not found
#include <cblas.h>
         ^~~~~~~~~
1 error generated.
make[2]: *** [src/openpose/CMakeFiles/openpose.dir/net/netCaffe.cpp.o] Error 1
make[2]: *** Waiting for unfinished jobs....
1 error generated.
make[2]: *** [src/openpose/CMakeFiles/openpose.dir/net/netOpenCv.cpp.o] Error 1
make[1]: *** [src/openpose/CMakeFiles/openpose.dir/all] Error 2
make[1]: *** Waiting for unfinished jobs....
-- -- CUDA is disabled. Building without it...
CMake Error at /Applications/CMake.app/Contents/share/cmake-3.23/Modules/FindPackageHandleStandardArgs.cmake:230 (message):
  Could NOT find vecLib (missing: vecLib_INCLUDE_DIR)
Call Stack (most recent call first):
  /Applications/CMake.app/Contents/share/cmake-3.23/Modules/FindPackageHandleStandardArgs.cmake:594 (_FPHSA_FAILURE_MESSAGE)
  cmake/Modules/FindvecLib.cmake:24 (find_package_handle_standard_args)
  cmake/Dependencies.cmake:135 (find_package)
  CMakeLists.txt:49 (include)


-- Configuring incomplete, errors occurred!
See also "/Users/a4923/Desktop/repositories/_construct/openpose/build/caffe/src/openpose_lib-build/CMakeFiles/CMakeOutput.log".
See also "/Users/a4923/Desktop/repositories/_construct/openpose/build/caffe/src/openpose_lib-build/CMakeFiles/CMakeError.log".
make[2]: *** [caffe/src/openpose_lib-stamp/openpose_lib-configure] Error 1
make[1]: *** [CMakeFiles/openpose_lib.dir/all] Error 2
make: *** [all] Error 2
```

</details>

 ~/ 부터 찾은 후에 다시 수정

```
# CMakeList.txt
if (APPLE)
  include_directories("/opt/homebrew/opt/openblas/include")
endif (APPLE)
```

</details>

<br>

#### **[issue 2]** : src/openpose/libopenpose.1.7.0.dylib 설정 오류 -> /opt/homebrew/Cellar/caffe/1.0_38/lib/libcaffe.dylib 로 경로 대체

<details>
<summary> issue_no2.log </summary>


<details>
<summary> error log </summary>

```bash
(base)  ✘   ~/Desktop/repositories/_construct/openpose/build   master ±  make -j`sysctl -n hw.logicalcpu`
[  0%] Performing configure step for 'openpose_lib'
CMake Deprecation Warning at CMakeLists.txt:1 (cmake_minimum_required):
  Compatibility with CMake < 2.8.12 will be removed from a future version of
  CMake.

  Update the VERSION argument <min> value or use a ...<max> suffix to tell
  CMake that the project does not need compatibility with older versions.


CMake Warning (dev) at cmake/Misc.cmake:32 (set):
  implicitly converting 'BOOLEAN' to 'STRING' type.
Call Stack (most recent call first):
  CMakeLists.txt:25 (include)
This warning is for project developers.  Use -Wno-dev to suppress it.

-- Found gflags  (include: /opt/homebrew/include, library: /opt/homebrew/lib/libgflags.dylib)
-- Found glog    (include: /opt/homebrew/include, library: /opt/homebrew/lib/libglog.dylib)
-- Found PROTOBUF Compiler: /opt/homebrew/bin/protoc
Consolidate compiler generated dependencies of target openpose
[  0%] Building CXX object src/openpose/CMakeFiles/openpose.dir/3d/cameraParameterReader.cpp.o
[  0%] Building CXX object src/openpose/CMakeFiles/openpose.dir/3d/defineTemplates.cpp.o
[  0%] Building CXX object src/openpose/CMakeFiles/openpose.dir/3d/poseTriangulation.cpp.o
[  1%] Building CXX object src/openpose/CMakeFiles/openpose.dir/3d/poseTriangulationPrivate.cpp.o
[  1%] Building CXX object src/openpose/CMakeFiles/openpose.dir/3d/jointAngleEstimation.cpp.o
[  1%] Building CXX object src/openpose/CMakeFiles/openpose.dir/calibration/gridPatternFunctions.cpp.o
[  2%] Building CXX object src/openpose/CMakeFiles/openpose.dir/calibration/cameraParameterEstimation.cpp.o
[  3%] Building CXX object src/openpose/CMakeFiles/openpose.dir/core/array.cpp.o
[  3%] Building CXX object src/openpose/CMakeFiles/openpose.dir/core/arrayCpuGpu.cpp.o
[  3%] Building CXX object src/openpose/CMakeFiles/openpose.dir/core/cvMatToOpInput.cpp.o
[  4%] Building CXX object src/openpose/CMakeFiles/openpose.dir/core/cvMatToOpOutput.cpp.o
[  4%] Building CXX object src/openpose/CMakeFiles/openpose.dir/core/datum.cpp.o
[  4%] Building CXX object src/openpose/CMakeFiles/openpose.dir/core/defineTemplates.cpp.o
[  5%] Building CXX object src/openpose/CMakeFiles/openpose.dir/core/gpuRenderer.cpp.o
[  5%] Building CXX object src/openpose/CMakeFiles/openpose.dir/core/keepTopNPeople.cpp.o
-- -- CUDA is disabled. Building without it...
CMake Error at /Applications/CMake.app/Contents/share/cmake-3.23/Modules/FindPackageHandleStandardArgs.cmake:230 (message):
  Could NOT find vecLib (missing: vecLib_INCLUDE_DIR)
Call Stack (most recent call first):
  /Applications/CMake.app/Contents/share/cmake-3.23/Modules/FindPackageHandleStandardArgs.cmake:594 (_FPHSA_FAILURE_MESSAGE)
  cmake/Modules/FindvecLib.cmake:24 (find_package_handle_standard_args)
  cmake/Dependencies.cmake:135 (find_package)
  CMakeLists.txt:49 (include)


-- Configuring incomplete, errors occurred!
See also "/Users/a4923/Desktop/repositories/_construct/openpose/build/caffe/src/openpose_lib-build/CMakeFiles/CMakeOutput.log".
See also "/Users/a4923/Desktop/repositories/_construct/openpose/build/caffe/src/openpose_lib-build/CMakeFiles/CMakeError.log".
make[2]: *** [caffe/src/openpose_lib-stamp/openpose_lib-configure] Error 1
make[1]: *** [CMakeFiles/openpose_lib.dir/all] Error 2
make[1]: *** Waiting for unfinished jobs....
[  6%] Building CXX object src/openpose/CMakeFiles/openpose.dir/core/keypointScaler.cpp.o
[  6%] Building CXX object src/openpose/CMakeFiles/openpose.dir/core/matrix.cpp.o
[  6%] Building CXX object src/openpose/CMakeFiles/openpose.dir/core/opOutputToCvMat.cpp.o
[  7%] Building CXX object src/openpose/CMakeFiles/openpose.dir/core/point.cpp.o
[  7%] Building CXX object src/openpose/CMakeFiles/openpose.dir/core/rectangle.cpp.o
[  7%] Building CXX object src/openpose/CMakeFiles/openpose.dir/core/renderer.cpp.o
[  8%] Building CXX object src/openpose/CMakeFiles/openpose.dir/core/scaleAndSizeExtractor.cpp.o
[  8%] Building CXX object src/openpose/CMakeFiles/openpose.dir/core/string.cpp.o
[  9%] Building CXX object src/openpose/CMakeFiles/openpose.dir/core/verbosePrinter.cpp.o
[  9%] Building CXX object src/openpose/CMakeFiles/openpose.dir/face/defineTemplates.cpp.o
[  9%] Building CXX object src/openpose/CMakeFiles/openpose.dir/face/faceDetector.cpp.o
[ 10%] Building CXX object src/openpose/CMakeFiles/openpose.dir/face/faceDetectorOpenCV.cpp.o
[ 10%] Building CXX object src/openpose/CMakeFiles/openpose.dir/face/faceExtractorCaffe.cpp.o
[ 11%] Building CXX object src/openpose/CMakeFiles/openpose.dir/face/faceExtractorNet.cpp.o
[ 11%] Building CXX object src/openpose/CMakeFiles/openpose.dir/face/faceCpuRenderer.cpp.o
[ 11%] Building CXX object src/openpose/CMakeFiles/openpose.dir/face/faceGpuRenderer.cpp.o
[ 12%] Building CXX object src/openpose/CMakeFiles/openpose.dir/face/faceRenderer.cpp.o
[ 12%] Building CXX object src/openpose/CMakeFiles/openpose.dir/face/renderFace.cpp.o
[ 12%] Building CXX object src/openpose/CMakeFiles/openpose.dir/filestream/bvhSaver.cpp.o
[ 13%] Building CXX object src/openpose/CMakeFiles/openpose.dir/filestream/cocoJsonSaver.cpp.o
[ 13%] Building CXX object src/openpose/CMakeFiles/openpose.dir/filestream/defineTemplates.cpp.o
[ 14%] Building CXX object src/openpose/CMakeFiles/openpose.dir/filestream/fileSaver.cpp.o
[ 14%] Building CXX object src/openpose/CMakeFiles/openpose.dir/filestream/fileStream.cpp.o
[ 14%] Building CXX object src/openpose/CMakeFiles/openpose.dir/filestream/heatMapSaver.cpp.o
[ 15%] Building CXX object src/openpose/CMakeFiles/openpose.dir/filestream/imageSaver.cpp.o
[ 15%] Building CXX object src/openpose/CMakeFiles/openpose.dir/filestream/jsonOfstream.cpp.o
[ 15%] Building CXX object src/openpose/CMakeFiles/openpose.dir/filestream/keypointSaver.cpp.o
[ 16%] Building CXX object src/openpose/CMakeFiles/openpose.dir/filestream/peopleJsonSaver.cpp.o
[ 16%] Building CXX object src/openpose/CMakeFiles/openpose.dir/filestream/udpSender.cpp.o
[ 17%] Building CXX object src/openpose/CMakeFiles/openpose.dir/filestream/videoSaver.cpp.o
[ 17%] Building CXX object src/openpose/CMakeFiles/openpose.dir/gpu/cuda.cpp.o
[ 17%] Building CXX object src/openpose/CMakeFiles/openpose.dir/gpu/gpu.cpp.o
[ 18%] Building CXX object src/openpose/CMakeFiles/openpose.dir/gpu/opencl.cpp.o
[ 18%] Building CXX object src/openpose/CMakeFiles/openpose.dir/gui/defineTemplates.cpp.o
[ 19%] Building CXX object src/openpose/CMakeFiles/openpose.dir/gui/frameDisplayer.cpp.o
[ 19%] Building CXX object src/openpose/CMakeFiles/openpose.dir/gui/gui.cpp.o
[ 19%] Building CXX object src/openpose/CMakeFiles/openpose.dir/gui/guiAdam.cpp.o
[ 20%] Building CXX object src/openpose/CMakeFiles/openpose.dir/gui/gui3D.cpp.o
[ 20%] Building CXX object src/openpose/CMakeFiles/openpose.dir/gui/guiInfoAdder.cpp.o
[ 20%] Building CXX object src/openpose/CMakeFiles/openpose.dir/hand/defineTemplates.cpp.o
[ 21%] Building CXX object src/openpose/CMakeFiles/openpose.dir/hand/handDetector.cpp.o
[ 21%] Building CXX object src/openpose/CMakeFiles/openpose.dir/hand/handDetectorFromTxt.cpp.o
[ 22%] Building CXX object src/openpose/CMakeFiles/openpose.dir/hand/handExtractorCaffe.cpp.o
[ 22%] Building CXX object src/openpose/CMakeFiles/openpose.dir/hand/handExtractorNet.cpp.o
[ 22%] Building CXX object src/openpose/CMakeFiles/openpose.dir/hand/handCpuRenderer.cpp.o
[ 23%] Building CXX object src/openpose/CMakeFiles/openpose.dir/hand/handGpuRenderer.cpp.o
[ 23%] Building CXX object src/openpose/CMakeFiles/openpose.dir/hand/handRenderer.cpp.o
[ 24%] Building CXX object src/openpose/CMakeFiles/openpose.dir/net/bodyPartConnectorBase.cpp.o
[ 24%] Building CXX object src/openpose/CMakeFiles/openpose.dir/hand/renderHand.cpp.o
[ 24%] Building CXX object src/openpose/CMakeFiles/openpose.dir/net/bodyPartConnectorBaseCL.cpp.o
[ 25%] Building CXX object src/openpose/CMakeFiles/openpose.dir/net/bodyPartConnectorCaffe.cpp.o
[ 25%] Building CXX object src/openpose/CMakeFiles/openpose.dir/net/maximumBase.cpp.o
[ 25%] Building CXX object src/openpose/CMakeFiles/openpose.dir/net/maximumCaffe.cpp.o
[ 26%] Building CXX object src/openpose/CMakeFiles/openpose.dir/net/netCaffe.cpp.o
[ 26%] Building CXX object src/openpose/CMakeFiles/openpose.dir/net/netOpenCv.cpp.o
[ 26%] Building CXX object src/openpose/CMakeFiles/openpose.dir/net/nmsBase.cpp.o
[ 27%] Building CXX object src/openpose/CMakeFiles/openpose.dir/net/nmsBaseCL.cpp.o
[ 27%] Building CXX object src/openpose/CMakeFiles/openpose.dir/net/nmsCaffe.cpp.o
[ 28%] Building CXX object src/openpose/CMakeFiles/openpose.dir/net/resizeAndMergeBase.cpp.o
[ 28%] Building CXX object src/openpose/CMakeFiles/openpose.dir/net/resizeAndMergeBaseCL.cpp.o
[ 28%] Building CXX object src/openpose/CMakeFiles/openpose.dir/net/resizeAndMergeCaffe.cpp.o
[ 29%] Building CXX object src/openpose/CMakeFiles/openpose.dir/pose/defineTemplates.cpp.o
[ 29%] Building CXX object src/openpose/CMakeFiles/openpose.dir/pose/poseCpuRenderer.cpp.o
[ 30%] Building CXX object src/openpose/CMakeFiles/openpose.dir/pose/poseExtractor.cpp.o
[ 30%] Building CXX object src/openpose/CMakeFiles/openpose.dir/pose/poseExtractorCaffe.cpp.o
[ 30%] Building CXX object src/openpose/CMakeFiles/openpose.dir/pose/poseExtractorNet.cpp.o
[ 31%] Building CXX object src/openpose/CMakeFiles/openpose.dir/pose/poseGpuRenderer.cpp.o
[ 31%] Building CXX object src/openpose/CMakeFiles/openpose.dir/pose/poseParameters.cpp.o
[ 31%] Building CXX object src/openpose/CMakeFiles/openpose.dir/pose/poseParametersRender.cpp.o
[ 32%] Building CXX object src/openpose/CMakeFiles/openpose.dir/pose/poseRenderer.cpp.o
[ 32%] Building CXX object src/openpose/CMakeFiles/openpose.dir/pose/renderPose.cpp.o
[ 33%] Building CXX object src/openpose/CMakeFiles/openpose.dir/producer/datumProducer.cpp.o
[ 33%] Building CXX object src/openpose/CMakeFiles/openpose.dir/producer/defineTemplates.cpp.o
[ 33%] Building CXX object src/openpose/CMakeFiles/openpose.dir/producer/flirReader.cpp.o
[ 34%] Building CXX object src/openpose/CMakeFiles/openpose.dir/producer/imageDirectoryReader.cpp.o
[ 34%] Building CXX object src/openpose/CMakeFiles/openpose.dir/producer/ipCameraReader.cpp.o
[ 34%] Building CXX object src/openpose/CMakeFiles/openpose.dir/producer/producer.cpp.o
[ 35%] Building CXX object src/openpose/CMakeFiles/openpose.dir/producer/spinnakerWrapper.cpp.o
[ 35%] Building CXX object src/openpose/CMakeFiles/openpose.dir/producer/videoCaptureReader.cpp.o
[ 36%] Building CXX object src/openpose/CMakeFiles/openpose.dir/producer/videoReader.cpp.o
[ 36%] Building CXX object src/openpose/CMakeFiles/openpose.dir/producer/webcamReader.cpp.o
[ 36%] Building CXX object src/openpose/CMakeFiles/openpose.dir/thread/defineTemplates.cpp.o
[ 37%] Building CXX object src/openpose/CMakeFiles/openpose.dir/tracking/defineTemplates.cpp.o
[ 37%] Building CXX object src/openpose/CMakeFiles/openpose.dir/tracking/personIdExtractor.cpp.o
[ 38%] Building CXX object src/openpose/CMakeFiles/openpose.dir/tracking/personTracker.cpp.o
[ 38%] Building CXX object src/openpose/CMakeFiles/openpose.dir/tracking/pyramidalLK.cpp.o
[ 39%] Building CXX object src/openpose/CMakeFiles/openpose.dir/utilities/errorAndLog.cpp.o
[ 39%] Building CXX object src/openpose/CMakeFiles/openpose.dir/unity/unityBinding.cpp.o
[ 39%] Building CXX object src/openpose/CMakeFiles/openpose.dir/utilities/fileSystem.cpp.o
[ 39%] Building CXX object src/openpose/CMakeFiles/openpose.dir/utilities/flagsToOpenPose.cpp.o
[ 40%] Building CXX object src/openpose/CMakeFiles/openpose.dir/utilities/keypoint.cpp.o
[ 40%] Building CXX object src/openpose/CMakeFiles/openpose.dir/utilities/openCv.cpp.o
[ 41%] Building CXX object src/openpose/CMakeFiles/openpose.dir/utilities/openCvPrivate.cpp.o
[ 41%] Building CXX object src/openpose/CMakeFiles/openpose.dir/utilities/profiler.cpp.o
[ 41%] Building CXX object src/openpose/CMakeFiles/openpose.dir/utilities/string.cpp.o
[ 42%] Building CXX object src/openpose/CMakeFiles/openpose.dir/wrapper/defineTemplates.cpp.o
[ 42%] Building CXX object src/openpose/CMakeFiles/openpose.dir/wrapper/wrapperAuxiliary.cpp.o
[ 42%] Building CXX object src/openpose/CMakeFiles/openpose.dir/wrapper/wrapperStructExtra.cpp.o
[ 43%] Building CXX object src/openpose/CMakeFiles/openpose.dir/wrapper/wrapperStructFace.cpp.o
[ 43%] Building CXX object src/openpose/CMakeFiles/openpose.dir/wrapper/wrapperStructGui.cpp.o
[ 44%] Building CXX object src/openpose/CMakeFiles/openpose.dir/wrapper/wrapperStructHand.cpp.o
[ 44%] Building CXX object src/openpose/CMakeFiles/openpose.dir/wrapper/wrapperStructInput.cpp.o
[ 44%] Building CXX object src/openpose/CMakeFiles/openpose.dir/wrapper/wrapperStructOutput.cpp.o
make[2]: *** No rule to make target `/usr/local/lib/libcaffe.dylib', needed by `src/openpose/libopenpose.1.7.0.dylib'.  Stop.
make[2]: *** Waiting for unfinished jobs....
[ 45%] Building CXX object src/openpose/CMakeFiles/openpose.dir/wrapper/wrapperStructPose.cpp.o
make[1]: *** [src/openpose/CMakeFiles/openpose.dir/all] Error 2
make: *** [all] Error 2
```

</details>

find로 경로 찾아서 Caffe_LIBS를 대체

```bash
find / -name libcaffe.dylib
# /opt/homebrew/lib/libcaffe.dylib
# /opt/homebrew/Cellar/caffe/1.0_38/lib/libcaffe.dylib
```

</details>
<br>

#### **[issue 3]**: configure 중 python, pybind 관련 오류 -> 해결 안되고 중요한 이슈가 아닌 것으로 판단, build 폴더 삭제하고 다시 진행

<details>
<summary> issue_no3.log </summary>


<details>
<summary> error log </summary>

```bash
CMake Error at 3rdparty/pybind11/tools/FindPythonLibsNew.cmake:95 (message):
  Python config failure:

  pyenv: python3.7: command not found

  

  The `python3.7' command exists in these Python versions:

    3.7.13
    3.7.13/envs/django-envs
    django-envs

  

  Note: See 'pyenv help global' for tips on allowing both

        python2 and python3 to be found.

Call Stack (most recent call first):
  3rdparty/pybind11/tools/pybind11Tools.cmake:16 (find_package)
  3rdparty/pybind11/CMakeLists.txt:33 (include)


Configuring incomplete, errors occurred!
See also "/Users/a4923/Desktop/repositories/_construct/openpose/build/CMakeFiles/CMakeOutput.log".
See also "/Users/a4923/Desktop/repositories/_construct/openpose/build/CMakeFiles/CMakeError.log".
```

</details>

1. uninstall
```bash
brew uninstall virtual-env
brew uninstall pyenv
```
2. iterm 자동 설정 해제
```bash
vi .zprofile
# env 부분 삭제

vi .zshrc
# virtual env, pyenv 부분 전부 삭제 (3줄 정도 되었음)
```
3. 사용자/`사용자명` 에 숨김폴더로 존재하는 `.pyenv` 삭제
사용자/a4923 에서 숨김폴더로 존재하는 (단축키: `shift + cmd + .`) 폴더 삭제

이후 pyenv 관련 에러 보이지 않음. 충돌 문제로 유추되는데 딱히 필요한게 아니었어서 지워버렸다.

<details>
<summary> 이후 error log </summary>

```bash
GCC detected, adding compile flags
Building CPU Only.
Found gflags  (include: /opt/homebrew/include, library: /opt/homebrew/lib/libgflags.dylib)
Found glog    (include: /opt/homebrew/include, library: /opt/homebrew/lib/libglog.dylib)
Could NOT find OpenMP_C (missing: OpenMP_C_FLAGS OpenMP_C_LIB_NAMES) 
Could NOT find OpenMP_CXX (missing: OpenMP_CXX_FLAGS OpenMP_CXX_LIB_NAMES) 
Could NOT find OpenMP (missing: OpenMP_C_FOUND OpenMP_CXX_FOUND) 
Caffe will be downloaded from source now. NOTE: This process might take several minutes depending
        on your internet connection.
Caffe has already been downloaded.
HEAD is now at 1807aada Added Ampere arch's (CUDA11)

Caffe will be built from source now.
Download the models.
Downloading BODY_25 model...
Model already exists.
Not downloading body (COCO) model
Not downloading body (MPI) model
Downloading face model...
Model already exists.
Downloading hand model...
Model already exists.
Models Downloaded.
CMake Error at 3rdparty/pybind11/tools/FindPythonLibsNew.cmake:95 (message):
  Python config failure:

Call Stack (most recent call first):
  3rdparty/pybind11/tools/pybind11Tools.cmake:16 (find_package)
  3rdparty/pybind11/CMakeLists.txt:33 (include)


Configuring incomplete, errors occurred!
See also "/Users/a4923/Desktop/repositories/_construct/openpose/build/CMakeFiles/CMakeOutput.log".
See also "/Users/a4923/Desktop/repositories/_construct/openpose/build/CMakeFiles/CMakeError.log".
```

</details>

```t
# 3rdparty/pybind11/tools/pybind11Tools.cmake
# 3rdparty/pybind11/tools/pybind11Tools.cmake:16 (find_package)

cmake_minimum_required(VERSION 2.8.12)

# Add a CMake parameter for choosing a desired Python version
if(NOT PYBIND11_PYTHON_VERSION)
  set(PYBIND11_PYTHON_VERSION "" CACHE STRING "Python version to use for compiling modules")
endif()

set(Python_ADDITIONAL_VERSIONS 3.7 3.6 3.5 3.4)
find_package(PythonLibsNew ${PYBIND11_PYTHON_VERSION} REQUIRED)     # 16 line
```

```t
# 3rdparty/pybind11/CMakeLists.txt:33
# 3rdparty/pybind11/CMakeLists.txt:33 (include)

include(pybind11Tools)  # 33 line

# Cache variables so pybind11_add_module can be used in parent projects
set(PYBIND11_INCLUDE_DIR "${CMAKE_CURRENT_LIST_DIR}/include" CACHE INTERNAL "")
set(PYTHON_INCLUDE_DIRS ${PYTHON_INCLUDE_DIRS} CACHE INTERNAL "")
set(PYTHON_LIBRARIES ${PYTHON_LIBRARIES} CACHE INTERNAL "")
set(PYTHON_MODULE_PREFIX ${PYTHON_MODULE_PREFIX} CACHE INTERNAL "")
set(PYTHON_MODULE_EXTENSION ${PYTHON_MODULE_EXTENSION} CACHE INTERNAL "")
set(PYTHON_VERSION_MAJOR ${PYTHON_VERSION_MAJOR} CACHE INTERNAL "")
set(PYTHON_VERSION_MINOR ${PYTHON_VERSION_MINOR} CACHE INTERNAL "")
```

이전까지 파이썬 버전 문제는 없었는데 왜... 이러는지 알 수 없음

<details>

<summary> 에러 로그를 확인해도 별게 없음 </summary>

```t
# cmake output
The system is: Darwin - shell-init: error retrieving current directory: getcwd: cannot        access parent directories: No such file or directory
21.5.0 - shell-init: error retrieving current directory: getcwd: cannot access parent         directories: No such file or directory
arm64
Compiling the C compiler identification source file "CMakeCCompilerId.c" succeeded.
Compiler: /Library/Developer/CommandLineTools/usr/bin/cc
Build flags:
Id flags: -c
8
The output was:
 0


 Compilation of the C compiler identification source "CMakeCCompilerId.c" produced "CMakeCCompilerId.o"

 The C compiler identification is AppleClang, found in "/Users/a4923/Desktop/repositories/_construct/openpose/build/CMakeFiles/3.23.2/CompilerIdC/CMakeCCompilerId.o"

 Compiling the CXX compiler identification source file "CMakeCXXCompilerId.cpp" succeeded.
 Compiler: /Library/Developer/CommandLineTools/usr/bin/c++
 Build flags:
 Id flags: -c

 The output was:
 0
```

```t
# cmake error log
Compiling the C compiler identification source file "CMakeCCompilerId.c" failed.
Compiler: /Library/Developer/CommandLineTools/usr/bin/cc
Build flags:
Id flags:
    5
The output was:
No such file or directory
    8    9
Compiling the CXX compiler identification source file "CMakeCXXCompilerId.cpp" failed.
Compiler: /Library/Developer/CommandLineTools/usr/bin/c++
Build flags:
Id flags:
   14
The output was:
1
ld: library not found for -lc++
clang: error: linker command failed with exit code 1 (use -v to see invocation)
   19   20
Detecting C OpenMP failed with the following output:
Change Dir: /Users/a4923/Desktop/repositories/_construct/openpose/build/CMakeFiles/CMakeTmp
   23
Run Build Command(s):/usr/bin/make -f Makefile cmTC_2b5ae/fast && /Library/Developer/CommandLineTools/usr/bin/make  -f CMakeFiles/cmTC_2b5ae.dir/build.make CMakeFiles/cmTC_2b5ae.dir/build
Building C object CMakeFiles/cmTC_2b5ae.dir/OpenMPTryFlag.c.o
```

</details>

</details>
<br>

#### **[issue 4]**: make percentage 50%까지는 올렸는데 이젠 뭐가 문제인지 모르겠다. -> CAFFE BUILD 해제, Example, LIBS 해제 후 Caffe_LIBS 를 <루트경로>/build/caffe/lib/libcaffe.dylib 로 변경하여 100% 빌드 완료

<details>
<summary> issue_no4.log </summary>


<details>
<summary> CAFFE_LIBS : /opt/homebrew/lib/libcaffe.dylib </summary>

```bash
(base)  ✘   ~/Desktop/repositories/_construct/openpose/build   master ±  make -j`sysctl -n hw.logicalcpu`
[  0%] Performing configure step for 'openpose_lib'
[  0%] Building CXX object src/openpose/CMakeFiles/openpose.dir/3d/defineTemplates.cpp.o
[  0%] Building CXX object src/openpose/CMakeFiles/openpose.dir/3d/jointAngleEstimation.cpp.o
[  1%] Building CXX object src/openpose/CMakeFiles/openpose.dir/3d/poseTriangulation.cpp.o
[  1%] Building CXX object src/openpose/CMakeFiles/openpose.dir/3d/poseTriangulationPrivate.cpp.o
[  1%] Building CXX object src/openpose/CMakeFiles/openpose.dir/calibration/cameraParameterEstimation.cpp.o
[  2%] Building CXX object src/openpose/CMakeFiles/openpose.dir/calibration/gridPatternFunctions.cpp.o
[  3%] Building CXX object src/openpose/CMakeFiles/openpose.dir/3d/cameraParameterReader.cpp.o
CMake Deprecation Warning at CMakeLists.txt:1 (cmake_minimum_required):
  Compatibility with CMake < 2.8.12 will be removed from a future version of
  CMake.

  Update the VERSION argument <min> value or use a ...<max> suffix to tell
  CMake that the project does not need compatibility with older versions.


[  3%] Building CXX object src/openpose/CMakeFiles/openpose.dir/core/array.cpp.o
CMake Warning (dev) at cmake/Misc.cmake:32 (set):
  implicitly converting 'BOOLEAN' to 'STRING' type.
Call Stack (most recent call first):
  CMakeLists.txt:25 (include)
This warning is for project developers.  Use -Wno-dev to suppress it.

-- Found gflags  (include: /opt/homebrew/include, library: /opt/homebrew/lib/libgflags.dylib)
-- Found glog    (include: /opt/homebrew/include, library: /opt/homebrew/lib/libglog.dylib)
-- Found PROTOBUF Compiler: /opt/homebrew/bin/protoc
[  3%] Building CXX object src/openpose/CMakeFiles/openpose.dir/core/arrayCpuGpu.cpp.o
[  3%] Building CXX object src/openpose/CMakeFiles/openpose.dir/core/cvMatToOpInput.cpp.o
[  4%] Building CXX object src/openpose/CMakeFiles/openpose.dir/core/cvMatToOpOutput.cpp.o
[  4%] Building CXX object src/openpose/CMakeFiles/openpose.dir/core/datum.cpp.o
[  4%] Building CXX object src/openpose/CMakeFiles/openpose.dir/core/defineTemplates.cpp.o
[  5%] Building CXX object src/openpose/CMakeFiles/openpose.dir/core/gpuRenderer.cpp.o
[  5%] Building CXX object src/openpose/CMakeFiles/openpose.dir/core/keepTopNPeople.cpp.o
[  5%] Building CXX object src/openpose/CMakeFiles/openpose.dir/core/keypointScaler.cpp.o
[  6%] Building CXX object src/openpose/CMakeFiles/openpose.dir/core/matrix.cpp.o
[  6%] Building CXX object src/openpose/CMakeFiles/openpose.dir/core/opOutputToCvMat.cpp.o
[  6%] Building CXX object src/openpose/CMakeFiles/openpose.dir/core/point.cpp.o
[  7%] Building CXX object src/openpose/CMakeFiles/openpose.dir/core/rectangle.cpp.o
[  7%] Building CXX object src/openpose/CMakeFiles/openpose.dir/core/renderer.cpp.o
-- -- CUDA is disabled. Building without it...
CMake Error at /Applications/CMake.app/Contents/share/cmake-3.23/Modules/FindPackageHandleStandardArgs.cmake:230 (message):
  Could NOT find vecLib (missing: vecLib_INCLUDE_DIR)
Call Stack (most recent call first):
  /Applications/CMake.app/Contents/share/cmake-3.23/Modules/FindPackageHandleStandardArgs.cmake:594 (_FPHSA_FAILURE_MESSAGE)
  cmake/Modules/FindvecLib.cmake:24 (find_package_handle_standard_args)
  cmake/Dependencies.cmake:135 (find_package)
  CMakeLists.txt:49 (include)


-- Configuring incomplete, errors occurred!
See also "/Users/a4923/Desktop/repositories/_construct/openpose/build/caffe/src/openpose_lib-build/CMakeFiles/CMakeOutput.log".
See also "/Users/a4923/Desktop/repositories/_construct/openpose/build/caffe/src/openpose_lib-build/CMakeFiles/CMakeError.log".
make[2]: *** [caffe/src/openpose_lib-stamp/openpose_lib-configure] Error 1
make[1]: *** [CMakeFiles/openpose_lib.dir/all] Error 2
make[1]: *** Waiting for unfinished jobs....
[  7%] Building CXX object src/openpose/CMakeFiles/openpose.dir/core/scaleAndSizeExtractor.cpp.o
[  8%] Building CXX object src/openpose/CMakeFiles/openpose.dir/core/string.cpp.o
[  8%] Building CXX object src/openpose/CMakeFiles/openpose.dir/core/verbosePrinter.cpp.o
[  8%] Building CXX object src/openpose/CMakeFiles/openpose.dir/face/defineTemplates.cpp.o
[  9%] Building CXX object src/openpose/CMakeFiles/openpose.dir/face/faceDetector.cpp.o
[  9%] Building CXX object src/openpose/CMakeFiles/openpose.dir/face/faceDetectorOpenCV.cpp.o
[  9%] Building CXX object src/openpose/CMakeFiles/openpose.dir/face/faceExtractorCaffe.cpp.o
[ 10%] Building CXX object src/openpose/CMakeFiles/openpose.dir/face/faceExtractorNet.cpp.o
[ 10%] Building CXX object src/openpose/CMakeFiles/openpose.dir/face/faceCpuRenderer.cpp.o
[ 10%] Building CXX object src/openpose/CMakeFiles/openpose.dir/face/faceGpuRenderer.cpp.o
[ 11%] Building CXX object src/openpose/CMakeFiles/openpose.dir/face/faceRenderer.cpp.o
[ 11%] Building CXX object src/openpose/CMakeFiles/openpose.dir/face/renderFace.cpp.o
[ 11%] Building CXX object src/openpose/CMakeFiles/openpose.dir/filestream/bvhSaver.cpp.o
[ 11%] Building CXX object src/openpose/CMakeFiles/openpose.dir/filestream/cocoJsonSaver.cpp.o
[ 12%] Building CXX object src/openpose/CMakeFiles/openpose.dir/filestream/defineTemplates.cpp.o
[ 12%] Building CXX object src/openpose/CMakeFiles/openpose.dir/filestream/fileSaver.cpp.o
[ 12%] Building CXX object src/openpose/CMakeFiles/openpose.dir/filestream/fileStream.cpp.o
[ 13%] Building CXX object src/openpose/CMakeFiles/openpose.dir/filestream/heatMapSaver.cpp.o
[ 13%] Building CXX object src/openpose/CMakeFiles/openpose.dir/filestream/imageSaver.cpp.o
[ 13%] Building CXX object src/openpose/CMakeFiles/openpose.dir/filestream/jsonOfstream.cpp.o
[ 14%] Building CXX object src/openpose/CMakeFiles/openpose.dir/filestream/keypointSaver.cpp.o
[ 14%] Building CXX object src/openpose/CMakeFiles/openpose.dir/filestream/peopleJsonSaver.cpp.o
[ 14%] Building CXX object src/openpose/CMakeFiles/openpose.dir/filestream/udpSender.cpp.o
[ 15%] Building CXX object src/openpose/CMakeFiles/openpose.dir/filestream/videoSaver.cpp.o
[ 15%] Building CXX object src/openpose/CMakeFiles/openpose.dir/gpu/cuda.cpp.o
[ 15%] Building CXX object src/openpose/CMakeFiles/openpose.dir/gpu/gpu.cpp.o
[ 16%] Building CXX object src/openpose/CMakeFiles/openpose.dir/gpu/opencl.cpp.o
[ 16%] Building CXX object src/openpose/CMakeFiles/openpose.dir/gui/defineTemplates.cpp.o
[ 16%] Building CXX object src/openpose/CMakeFiles/openpose.dir/gui/frameDisplayer.cpp.o
[ 17%] Building CXX object src/openpose/CMakeFiles/openpose.dir/gui/gui.cpp.o
[ 17%] Building CXX object src/openpose/CMakeFiles/openpose.dir/gui/guiAdam.cpp.o
[ 17%] Building CXX object src/openpose/CMakeFiles/openpose.dir/gui/gui3D.cpp.o
[ 17%] Building CXX object src/openpose/CMakeFiles/openpose.dir/hand/defineTemplates.cpp.o
[ 18%] Building CXX object src/openpose/CMakeFiles/openpose.dir/gui/guiInfoAdder.cpp.o
[ 18%] Building CXX object src/openpose/CMakeFiles/openpose.dir/hand/handDetector.cpp.o
[ 18%] Building CXX object src/openpose/CMakeFiles/openpose.dir/hand/handDetectorFromTxt.cpp.o
[ 19%] Building CXX object src/openpose/CMakeFiles/openpose.dir/hand/handExtractorCaffe.cpp.o
[ 19%] Building CXX object src/openpose/CMakeFiles/openpose.dir/hand/handExtractorNet.cpp.o
[ 19%] Building CXX object src/openpose/CMakeFiles/openpose.dir/hand/handCpuRenderer.cpp.o
[ 20%] Building CXX object src/openpose/CMakeFiles/openpose.dir/hand/handGpuRenderer.cpp.o
[ 20%] Building CXX object src/openpose/CMakeFiles/openpose.dir/hand/handRenderer.cpp.o
[ 20%] Building CXX object src/openpose/CMakeFiles/openpose.dir/hand/renderHand.cpp.o
[ 21%] Building CXX object src/openpose/CMakeFiles/openpose.dir/net/bodyPartConnectorBase.cpp.o
[ 21%] Building CXX object src/openpose/CMakeFiles/openpose.dir/net/bodyPartConnectorBaseCL.cpp.o
[ 21%] Building CXX object src/openpose/CMakeFiles/openpose.dir/net/bodyPartConnectorCaffe.cpp.o
[ 22%] Building CXX object src/openpose/CMakeFiles/openpose.dir/net/maximumBase.cpp.o
[ 22%] Building CXX object src/openpose/CMakeFiles/openpose.dir/net/maximumCaffe.cpp.o
[ 22%] Building CXX object src/openpose/CMakeFiles/openpose.dir/net/netCaffe.cpp.o
[ 23%] Building CXX object src/openpose/CMakeFiles/openpose.dir/net/netOpenCv.cpp.o
[ 23%] Building CXX object src/openpose/CMakeFiles/openpose.dir/net/nmsBase.cpp.o
[ 23%] Building CXX object src/openpose/CMakeFiles/openpose.dir/net/nmsBaseCL.cpp.o
[ 24%] Building CXX object src/openpose/CMakeFiles/openpose.dir/net/nmsCaffe.cpp.o
[ 24%] Building CXX object src/openpose/CMakeFiles/openpose.dir/net/resizeAndMergeBase.cpp.o
[ 24%] Building CXX object src/openpose/CMakeFiles/openpose.dir/net/resizeAndMergeBaseCL.cpp.o
[ 25%] Building CXX object src/openpose/CMakeFiles/openpose.dir/net/resizeAndMergeCaffe.cpp.o
[ 25%] Building CXX object src/openpose/CMakeFiles/openpose.dir/pose/defineTemplates.cpp.o
[ 25%] Building CXX object src/openpose/CMakeFiles/openpose.dir/pose/poseCpuRenderer.cpp.o
[ 26%] Building CXX object src/openpose/CMakeFiles/openpose.dir/pose/poseExtractor.cpp.o
[ 26%] Building CXX object src/openpose/CMakeFiles/openpose.dir/pose/poseExtractorCaffe.cpp.o
[ 26%] Building CXX object src/openpose/CMakeFiles/openpose.dir/pose/poseExtractorNet.cpp.o
[ 26%] Building CXX object src/openpose/CMakeFiles/openpose.dir/pose/poseGpuRenderer.cpp.o
[ 27%] Building CXX object src/openpose/CMakeFiles/openpose.dir/pose/poseParameters.cpp.o
[ 27%] Building CXX object src/openpose/CMakeFiles/openpose.dir/pose/poseParametersRender.cpp.o
[ 27%] Building CXX object src/openpose/CMakeFiles/openpose.dir/pose/poseRenderer.cpp.o
[ 28%] Building CXX object src/openpose/CMakeFiles/openpose.dir/pose/renderPose.cpp.o
[ 28%] Building CXX object src/openpose/CMakeFiles/openpose.dir/producer/datumProducer.cpp.o
[ 28%] Building CXX object src/openpose/CMakeFiles/openpose.dir/producer/defineTemplates.cpp.o
[ 29%] Building CXX object src/openpose/CMakeFiles/openpose.dir/producer/flirReader.cpp.o
[ 29%] Building CXX object src/openpose/CMakeFiles/openpose.dir/producer/imageDirectoryReader.cpp.o
[ 29%] Building CXX object src/openpose/CMakeFiles/openpose.dir/producer/ipCameraReader.cpp.o
[ 30%] Building CXX object src/openpose/CMakeFiles/openpose.dir/producer/producer.cpp.o
[ 30%] Building CXX object src/openpose/CMakeFiles/openpose.dir/producer/spinnakerWrapper.cpp.o
[ 30%] Building CXX object src/openpose/CMakeFiles/openpose.dir/producer/videoCaptureReader.cpp.o
[ 31%] Building CXX object src/openpose/CMakeFiles/openpose.dir/producer/videoReader.cpp.o
[ 31%] Building CXX object src/openpose/CMakeFiles/openpose.dir/producer/webcamReader.cpp.o
[ 31%] Building CXX object src/openpose/CMakeFiles/openpose.dir/thread/defineTemplates.cpp.o
[ 32%] Building CXX object src/openpose/CMakeFiles/openpose.dir/tracking/defineTemplates.cpp.o
[ 32%] Building CXX object src/openpose/CMakeFiles/openpose.dir/tracking/personIdExtractor.cpp.o
[ 32%] Building CXX object src/openpose/CMakeFiles/openpose.dir/tracking/personTracker.cpp.o
[ 33%] Building CXX object src/openpose/CMakeFiles/openpose.dir/tracking/pyramidalLK.cpp.o
[ 33%] Building CXX object src/openpose/CMakeFiles/openpose.dir/unity/unityBinding.cpp.o
[ 33%] Building CXX object src/openpose/CMakeFiles/openpose.dir/utilities/errorAndLog.cpp.o
[ 34%] Building CXX object src/openpose/CMakeFiles/openpose.dir/utilities/fileSystem.cpp.o
[ 34%] Building CXX object src/openpose/CMakeFiles/openpose.dir/utilities/flagsToOpenPose.cpp.o
[ 34%] Building CXX object src/openpose/CMakeFiles/openpose.dir/utilities/keypoint.cpp.o
[ 34%] Building CXX object src/openpose/CMakeFiles/openpose.dir/utilities/openCv.cpp.o
[ 35%] Building CXX object src/openpose/CMakeFiles/openpose.dir/utilities/openCvPrivate.cpp.o
[ 35%] Building CXX object src/openpose/CMakeFiles/openpose.dir/utilities/profiler.cpp.o
[ 35%] Building CXX object src/openpose/CMakeFiles/openpose.dir/utilities/string.cpp.o
[ 36%] Building CXX object src/openpose/CMakeFiles/openpose.dir/wrapper/defineTemplates.cpp.o
[ 36%] Building CXX object src/openpose/CMakeFiles/openpose.dir/wrapper/wrapperAuxiliary.cpp.o
[ 36%] Building CXX object src/openpose/CMakeFiles/openpose.dir/wrapper/wrapperStructExtra.cpp.o
[ 37%] Building CXX object src/openpose/CMakeFiles/openpose.dir/wrapper/wrapperStructFace.cpp.o
[ 37%] Building CXX object src/openpose/CMakeFiles/openpose.dir/wrapper/wrapperStructGui.cpp.o
[ 37%] Building CXX object src/openpose/CMakeFiles/openpose.dir/wrapper/wrapperStructHand.cpp.o
[ 38%] Building CXX object src/openpose/CMakeFiles/openpose.dir/wrapper/wrapperStructInput.cpp.o
[ 38%] Building CXX object src/openpose/CMakeFiles/openpose.dir/wrapper/wrapperStructOutput.cpp.o
[ 38%] Building CXX object src/openpose/CMakeFiles/openpose.dir/wrapper/wrapperStructPose.cpp.o
[ 39%] Linking CXX shared library libopenpose.dylib
[ 39%] Built target openpose
make: *** [all] Error 2
```

</details>


<details>

<summary> [변경] CAFFE_LIBS : `/opt/homebrew/Cellar/caffe/1.0_38/lib/libcaffe.dylib` </summary>

```bash
(base)  ✘   ~/Desktop/repositories/_construct/openpose/build   master ±  make -j`sysctl -n hw.logicalcpu`
[  0%] Performing configure step for 'openpose_lib'
CMake Deprecation Warning at CMakeLists.txt:1 (cmake_minimum_required):
  Compatibility with CMake < 2.8.12 will be removed from a future version of
  CMake.

  Update the VERSION argument <min> value or use a ...<max> suffix to tell
  CMake that the project does not need compatibility with older versions.


CMake Warning (dev) at cmake/Misc.cmake:32 (set):
  implicitly converting 'BOOLEAN' to 'STRING' type.
Call Stack (most recent call first):
  CMakeLists.txt:25 (include)
This warning is for project developers.  Use -Wno-dev to suppress it.

-- Found gflags  (include: /opt/homebrew/include, library: /opt/homebrew/lib/libgflags.dylib)
-- Found glog    (include: /opt/homebrew/include, library: /opt/homebrew/lib/libglog.dylib)
-- Found PROTOBUF Compiler: /opt/homebrew/bin/protoc
Consolidate compiler generated dependencies of target openpose
[  1%] Linking CXX shared library libopenpose.dylib
[ 39%] Built target openpose
[ 39%] Building CXX object examples/deprecated/CMakeFiles/tutorial_add_module_custom_post_processing.bin.dir/tutorial_add_module_custom_post_processing.cpp.o
[ 39%] Building CXX object examples/calibration/CMakeFiles/calibration.bin.dir/calibration.cpp.o
[ 40%] Building CXX object examples/deprecated/CMakeFiles/tutorial_api_thread_1_user_processing_function.bin.dir/tutorial_api_thread_1_user_processing_function.cpp.o
[ 40%] Building CXX object examples/openpose/CMakeFiles/openpose.bin.dir/openpose.cpp.o
[ 40%] Building CXX object src/openpose/core/CMakeFiles/openpose_core.dir/array.cpp.o
[ 40%] Building CXX object examples/deprecated/CMakeFiles/tutorial_api_thread_2_user_input_processing_output_and_datum.bin.dir/tutorial_api_thread_2_user_input_processing_output_and_datum.cpp.o
[ 40%] Building CXX object examples/tutorial_api_cpp/CMakeFiles/01_body_from_image_default.bin.dir/01_body_from_image_default.cpp.o
[ 40%] Linking CXX executable openpose.bin
[ 40%] Built target openpose.bin
[ 41%] Building CXX object src/openpose/core/CMakeFiles/openpose_core.dir/arrayCpuGpu.cpp.o
[ 42%] Linking CXX executable calibration.bin
[ 42%] Built target calibration.bin
[ 42%] Building CXX object examples/tutorial_api_cpp/CMakeFiles/02_whole_body_from_image_default.bin.dir/02_whole_body_from_image_default.cpp.o
[ 42%] Linking CXX executable 01_body_from_image_default.bin
[ 42%] Built target 01_body_from_image_default.bin
[ 42%] Building CXX object examples/tutorial_api_cpp/CMakeFiles/03_keypoints_from_image.bin.dir/03_keypoints_from_image.cpp.o
[ 42%] Linking CXX executable tutorial_api_thread_1_user_processing_function.bin
[ 42%] Built target tutorial_api_thread_1_user_processing_function.bin
-- -- CUDA is disabled. Building without it...
CMake Error at /Applications/CMake.app/Contents/share/cmake-3.23/Modules/FindPackageHandleStandardArgs.cmake:230 (message):
  Could NOT find vecLib (missing: vecLib_INCLUDE_DIR)
Call Stack (most recent call first):
  /Applications/CMake.app/Contents/share/cmake-3.23/Modules/FindPackageHandleStandardArgs.cmake:594 (_FPHSA_FAILURE_MESSAGE)
  cmake/Modules/FindvecLib.cmake:24 (find_package_handle_standard_args)
  cmake/Dependencies.cmake:135 (find_package)
  CMakeLists.txt:49 (include)


-- Configuring incomplete, errors occurred!
See also "/Users/a4923/Desktop/repositories/_construct/openpose/build/caffe/src/openpose_lib-build/CMakeFiles/CMakeOutput.log".
See also "/Users/a4923/Desktop/repositories/_construct/openpose/build/caffe/src/openpose_lib-build/CMakeFiles/CMakeError.log".
make[2]: *** [caffe/src/openpose_lib-stamp/openpose_lib-configure] Error 1
make[1]: *** [CMakeFiles/openpose_lib.dir/all] Error 2
make[1]: *** Waiting for unfinished jobs....
[ 43%] Building CXX object examples/tutorial_api_cpp/CMakeFiles/04_keypoints_from_images.bin.dir/04_keypoints_from_images.cpp.o
[ 43%] Building CXX object src/openpose/core/CMakeFiles/openpose_core.dir/cvMatToOpInput.cpp.o
[ 43%] Building CXX object src/openpose/core/CMakeFiles/openpose_core.dir/cvMatToOpOutput.cpp.o
[ 44%] Linking CXX executable tutorial_api_thread_2_user_input_processing_output_and_datum.bin
[ 44%] Built target tutorial_api_thread_2_user_input_processing_output_and_datum.bin
[ 45%] Building CXX object src/openpose/core/CMakeFiles/openpose_core.dir/datum.cpp.o
[ 46%] Linking CXX executable 02_whole_body_from_image_default.bin
[ 46%] Built target 02_whole_body_from_image_default.bin
[ 46%] Building CXX object src/openpose/core/CMakeFiles/openpose_core.dir/defineTemplates.cpp.o
[ 46%] Linking CXX executable 03_keypoints_from_image.bin
[ 46%] Built target 03_keypoints_from_image.bin
[ 46%] Building CXX object src/openpose/core/CMakeFiles/openpose_core.dir/gpuRenderer.cpp.o
[ 47%] Building CXX object src/openpose/core/CMakeFiles/openpose_core.dir/keepTopNPeople.cpp.o
[ 47%] Linking CXX executable 04_keypoints_from_images.bin
[ 47%] Building CXX object src/openpose/core/CMakeFiles/openpose_core.dir/keypointScaler.cpp.o
[ 47%] Built target 04_keypoints_from_images.bin
[ 47%] Building CXX object src/openpose/core/CMakeFiles/openpose_core.dir/matrix.cpp.o
[ 48%] Building CXX object src/openpose/core/CMakeFiles/openpose_core.dir/opOutputToCvMat.cpp.o
[ 48%] Building CXX object src/openpose/core/CMakeFiles/openpose_core.dir/point.cpp.o
[ 48%] Building CXX object src/openpose/core/CMakeFiles/openpose_core.dir/rectangle.cpp.o
[ 49%] Building CXX object src/openpose/core/CMakeFiles/openpose_core.dir/renderer.cpp.o
[ 49%] Building CXX object src/openpose/core/CMakeFiles/openpose_core.dir/scaleAndSizeExtractor.cpp.o
[ 49%] Building CXX object src/openpose/core/CMakeFiles/openpose_core.dir/string.cpp.o
[ 50%] Building CXX object src/openpose/core/CMakeFiles/openpose_core.dir/verbosePrinter.cpp.o
[ 50%] Linking CXX shared library libopenpose_core.dylib
[ 50%] Built target openpose_core
[ 50%] Linking CXX executable tutorial_add_module_custom_post_processing.bin
[ 50%] Built target tutorial_add_module_custom_post_processing.bin
make: *** [all] Error 2
```

</details>

</details>

