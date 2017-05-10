# Ubuntu 16.04 설치

우분투 배포판 다운로드, USB부팅 디스크 제작   
윈도우에서 해야함.  
/는 OS booting 전용 영역 package들을 깔걸 생각해서 64G정도 잡음  
swap 영역은 윈도우의 cache에 해당 32G  
나머지 partition은 /home으로 몰아줌(이럼 사용자ᅟdata는 따로 백업가능)


## Ubuntu에 tensorflow(GPU) 돌리기

기본적으로는
[ubuntu에 tensorflow구동](http://ishuca.tistory.com/m/post/entry/Ubuntu-1404-%EC%97%90%EC%84%9C-%EC%95%84%EB%82%98%EC%BD%98%EB%8B%A4%EC%97%90-Tensorflow-%EC%84%A4%EC%B9%98%ED%95%98%EA%B8%B0)

이걸따라가고,

[terryum 블로그](http://terryum.io/ml_practice/2016/05/15/UbuntuSetup/)
도 참고 할것.

[nvidia 공식 홈피](http://www.nvidia.com/download/driverResults.aspx/104284/en-us)에서 정식 그래픽 드라이버를 깔고,  
CUDA를 깔땐 driver, openGL전부 안깐다. cuda랑 샘플만 깔아줌.  

[텐서플로우 공식페이지](https://www.tensorflow.org/versions/r0.9/get_started/os_setup.html#anaconda-installation) 를 참고하여  
anaconda를 python 2.7버젼과 python 3.5버젼 용으로 따로 깔고,  
conda 가상 환경을 각각 만들어줌.  
마찬가지로 conda 가상 환경 안에서 알맞는 tensorflow version을 각각 Install.  
(pip version이 안맞는다는 error도 conda깔고 그안에서 이것저것 깔면 해결)   

pycharm 실행을 conda 가상 환경 terminal 에서 해주면 됨


현재 내 컴 환경은

cd pycharm/bin
bash pycharm.sh

참고한 [블로그](http://yeramee.tistory.com/m/post/1)

Pycharm 을 사용하는 동안 terminal을 닫으면 안됨.



## GPU driver가 사라졌을때
!!!요부분은 아직 경험하지 못한 부분 우분투 14.04를 쓸 땐 CUDA driver를 썼지만,  
16에서는 따로 드라이버를 잡았으로, 그 드라이버를 새로 깔아서 해결해보자.  
그전에 이 현상이 안 일어나야겠지.... 자동 업데하다 nvidia랑 충돌나서 생기는 문제인 거 같은데.  

~~일단 피씨 본체의 reset 버튼으로 강제 리부팅을 하면 날아 가는듯하다~~  
~~(모니터 Input source 때문에 몇번 리부팅했을때 괜찮은 걸보면 GPU 컴퓨팅 중에 하면 날아가는듯)~~

그러면 CUDA만 다시 설치하면됨

1. 재부팅후 ctrl+alt+F1으로 가상터미널 접속
2. sudo stop lightdm, sudo service lightdm stop
3. su sh cuda_7.5.18_linux.run(nvidia driver yes, openGL no)
4. sudo reboot 하면 드라이버는 가동
5. CUDNN다시 카피(sudo nautilus로 관리자 탐색기 띄우고 Home/cuda에 있는 파일들을 usr/local/cuda로 각각 옮긴다.)


## allensdk
현재 document에 python notebook file들을 받아 test중.
그중 biophysical model은 [NEURON](http://www.neuron.yale.edu./neuron/download/compile_linux#step2)이 있어야 기능함.  
iv(interview)라는 것은 먼저 설치해야하며  
$HOME에 neuron/iv, neuron 디렉토리에 파일을 두개 풀고 설치함.  
./configure 하면 X11이 없다고 오류가 나는데, 


```bash 
sudo apt-get install libxext-dev
sudo apt-get install mercurial bison flex automake libtool g++ rpm
sudo apt-get install libxext-dev libncurses-dev python-dev
sudo apt-get install cython alien
sudo apt-get install xfonts-100dpi #helvetica
sudo apt-get install xfonts-75dpi #for some special idraw I like
```


들을 깔아주면 해결.  
~~다 깔고 나서 nrniv나 nrngui는 안먹지만(명령어가 없다고 나옴)~~

Python에 neuron package은 불러와짐. allen conda env에 설치함.  
```shell
~/neuron/nrn/bin$ bash nrngui -python
```


## ubuntu file sharing with OSX
root 계정으로 들어가서 samba를 깔아준다.
```bash
sudo su
apt-get install gksu
apt-get install libcups2 samba samba-common
```
samba setting file을 열어서 user 계정을 팔수있게 한다.
```bash
gedit /etc/samba/smb.conf
# editor에서
  security = user
  username map = /etc/samba/smbusers
# 적당히 추가
service smbd restart #cmd line에서 samba restart
```
그런 다음 samba user를 추가해준다
```bash
smbpasswd -a <username>
service smbd restart
```
이제 OSX에서 smb 주소 접근가능. sharing folder를 탐색기해서 정해줘도됨

## ubuntu mouse point speed control

```bash
xset q | grep -A 1 Pointer
# 현재 mouse pointer 가속도 값과 역치값이 나옴.
xset m 8 8
# 가속값을 8, 역치는 8픽셀로 설정
```
이것을 우분투 시작할 때 실행하기 위해서는  
rc.local에 명령을 해줘야함  
```bash
sudo vim /etc/rc.local
```
다음과 같이 추가함  
```bash
xset m 8 2
```
가속값을 8, 역치는 2픽셀로.



