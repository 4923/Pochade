## AlphaPose

- 중국에서 제작한 Top-Down 방식의 자세 추정 시스템
- 실시간 추적 가능 (23FPS)
- 개발 환경
    - PyTorch 기반
    - Linux, Windows 모두 지원


### For Starters

- [참고](https://eehoeskrap.tistory.com/299)

1. 기본세팅
```bash
# Git repo
git clone -b pytorch https://github.com/MVIG-SJTU/AlphaPose.git
# pytorch M1용 GPU Nightly version
pip3 install --pre torch torchvision torchaudio --extra-index-url https://download.pytorch.org/whl/nightly/cpu
# 기본 패키지들 설치
conda install jupyter pandas numpy matplotlib scikit-learn tqdm 

# 설정: https://github.com/MVIG-SJTU/AlphaPose/blob/master/docs/INSTALL.md
export PATH=/usr/local/cuda/bin/:$PATH
export LD_LIBRARY_PATH=/usr/local/cuda/lib64/:$LD_LIBRARY_PATH
pip install cython
sudo apt-get install libyaml-dev
python3 setup.py build develop --user
```

2. 모델 설정

- 객체감지모델을 수동으로 다운로드 후 `detector/yolo/data` 경로에 위치 ([다운로드: google drive](https://drive.google.com/open?id=1D47msNOOiJKvPOXlnpyzdKA3k6E97NTC))
- Model Zoo에서 원하는 모델 다운로드 후 `pretrained_models` 경로에 위치 ([model_zoo](https://github.com/MVIG-SJTU/AlphaPose/blob/master/docs/MODEL_ZOO.md))
    - 가장 권장되는 multi domain model (resnet50 based) 사용 ([github](https://github.com/MVIG-SJTU/AlphaPose/blob/master/docs/MODEL_ZOO.md#multi-domain-models-strongly-recommended))
    - 더 정확하고 유연한 활용을 기대할 수 있음

아니 이 사전학습모델을 다운받아와야하는데 바이두에서만 받아올 수 있고 나는 중국어를 모르는데 가입을 하려면 휴대폰 번호가 필요하고 한국 번호로는 가입이 안됨

> FB의 detectron으로 이동
