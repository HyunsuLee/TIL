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

2단계에서 lightdm을 stop하면 가상타미널도 stop되면서 black screen이 되는 경우가 있다.  
그럴땐 일단
```bash
sudo service lightdm start
```
를 깜깜이 상태에서 쳐서 화면을 살리고, 다음 단계를 진행한다.  
이 현상의 원인은 가상터미널에서도 graphic mode가 작동되어서 그런듯 하다.  
그걸 끄기 위해서 grub을 재설정 해줘야함. 
```bash
sudo nautilus
```
관리자 탐색기에서  
/etc/default/grub  을 backup한다.
```bash
# GRUB_TERMINAL=console
# GRUB_GFXMODE=640x480
```
으로 터미널 그래픽 모드를 비활성화시킨다. 
```bash
sudo update-grub
```
그럽을 업데해주고 sudo vim grub에서 다음과 같은 라인을 추가한다.  
```bash
gfxpayload=nomodeset
$~sudo reboot
```
그럼 이제 가상터미널에서 lightdm을 stop해도 화면이 보인다.


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

## ubuntu pycharm 설치

```bash
sudo sh -c 'echo "deb http://archive.getdeb.net/ubuntu $(lsb_release -sc)-getdeb apps" >> /etc/apt/sources.list.d/getdeb.list'
wget -q -O - http://archive.getdeb.net/getdeb-archive.key | sudo apt-key add -
sudo apt update

sudo apt install pycharm
```

## ubuntu control alt key 바꾸기
맥과 우분투를 번갈아 쓰다보면, control key 위치가 자주 헷갈린다. 맥의 command가 더 안쪽에 있기 때문  
이를 해결하기 위해 control, alt key mapping

```bash
~$ code ~/.Xmodmap
```
.Xmodmap파일를 home에 만들어준다.
```bash
clear control
clear mod1
keycode 37 = Alt_L Meta_L
keycode 64 = Control_L
add control = Control_L Control_R
add mod1 = Alt_L Meta_L
```
가끔 keycode가 다를수 있다. 이를 위해서 
```bash
~$ xev
```
를 실행하면 작은 하얀창이 뜨고 이 창에서 control key를 누르면 터미널에서 
```bash
KeyPress event, serial 37, synthetic NO, window 0x4c00001,
    root 0x1f7, subw 0x0, time 953652796, (120,149), root:(1945,253),
    state 0x0, keycode 37 (keysym 0xffe3, Control_L), same_screen YES,
    XLookupString gives 0 bytes: 
    XmbLookupString gives 0 bytes: 
    XFilterEvent returns: False
```
라고 뜸. 여기서 keycode확인하고 바꿔준다. 
자꾸 원래대로 바뀌는데, 그 이유는 .bashrc가 터미널 열때마다 실행되면서 keymap을 초기화하기 때문인듯.
하여 .bashrc에 추가해줌
```bash
code ~/.bashrc
```
로 열고
```
xmodmap ~/.Xmodmap
```
을 추가함.
이래도 자꾸 원상복구됨...keymap를 리셋하는 프로그램이 있나봄.  
이건 그냥 포기함..

## ubuntu 16.04 matlab issue
우분투 업글 이후 matlab이 crush 남.
```bash
locate libstdc++.so.6
```
를 쳐서, matlab 위치를 찾는다. 나같은 경우
```bash
/usr/local/MATLAB/R2016a/sys/os/glnxa64/libstdc++.so.6
/usr/local/MATLAB/R2016a/sys/os/glnxa64/libstdc++.so.6.0.17
```
이었음.
```bash
/usr/local/MATLAB/R2016a/sys/os/glnxa64
```
로 가서 파일이름을 바꿔줌.
```bash
sudo mv libstdc++.so.6 libstdc++.so.6.old
sudo mv libstdc++.so.6.0.17 libstdc++.so.6.0.17.old 
```
이렇게 하면 matlab의 rendering이 제대로 잡힘.


## Ubuntu ssh server setting

맥과 우분투 모두 ssh는 기본으로 인스톨 되어있다.  
가장 간단하게 서버에 접속하는 방법은 클라이언트에서 다음과 같이 입력하는 거다.
```bash
ssh username@hostip
```
나는 서버의 내 계정과 서버 ip를 바로 사용했지만 서버에서 사용자 list를 관리할 수 있다.  
### RSA key 만들고 쓰기.
```bash
client $ ssh-keygen -t rsa
```
rsa 키를 만들어주면.  
* ~/.ssh/id_rsa : private key
* ~/.ssh/id_rsa.pub : public key  
가 만들어진다. (내 맥북에는 이미 있음)  
이중 public key를 서버로 카피해야함.  
```bash
client $ scp .ssh/id_rsa.pub yourid@server_ip:.ssh/authorized_keys
```
클라이언트 키에 pass phrase을 입력했을 경우  
ssh server에 접속할때마다 rsa key pass phrase를 입력하는 게 귀찮다면 
```bash
client $ ssh-add
     input : pass phrase?
```
해두면 안 묻더라.  

### ssh server remote setting  
외부에서도 서버에 접속하기 위해서는 공유기 port forwarding을 쓰면 된다.  
공유기 setting에서 서버 ip를 잡고, port를 ~~외부~~, 내부 모두 22(ssh)로 잡아줌.
(대학 전산망이 port 22는 filtering해서 안됨.)  
closed port 중에 숫자가 큰걸 외부 port로 잡았음(port ????)  

이러면 공유기 ip로 접속된 ssh가 공유기 하의 서버 ip로 넘어감.  
```bash
ssh -p ???? your_id@server_ip
```
로 간단하게 외부망에서도 접근할 수 있음.(집에서 테스트해볼것)  

## Remote setting하면 보안에 신경써야함.
### sshd config file 
etc/ssh/sshd_config에 다음과 같은 세팅을 제대로 해두자.
```bash
Protocol 2 # Protocol 1은 보안이슈가 있다. 보통 기본임.
AllowUsers root vivek jerry #특정 유저만 허용, DenyUsers를 써사 ban하는 방법도.
ClientAliveInterval 300 # 300sec까지만 허용.
ClientAliveCountMax 0 
IgnoreRhosts yes #기본으로 되어있음.
HostbasedAuthentication no #기본으로 되어있음.
Banner /etc/issue.net #접속시 경고 배너 띄우기. 난 안 쓴다.
PermitEmptyPasswords no #기본으로 되어있음.
LogLevel INFO #기본으로 되어있음.
PasswordAuthentication no #RSA key를 쓰면 이건 꺼두자.
```

### ssh server log 시도 파일 보기
그중에서 실패한 시도들만 따로 보는 방법
```bash
server $ tail /var/log/auth.log -n 100 | grep 'Failed'
```


__jupyter notebook__  
참고 [블로그](http://www.justinkiggins.com/blog/zero-configuration-remote-jupyter-server/)  
서버에서 주피터를 브라우저 없이 띄운다
```bash
server $ jupyter notebook --no-browser --port=8888
```
port 8888로 띄운걸, client에서 ssh로 접속해서 땡겨옴.  
```bash
client $ ssh -NL 8888:localhost:8888 your_id@server_ip
```
다음 내 브라우저에서 localhost:8888을 띄우면 server의 주피터가 뜸.

## Using tmux
우분투서버에 터미널로 접속한다음. 한 터미널 윈도우에서 멀티 세션을 돌리고 싶을때 쓴다.  
참고 [블로그](http://www.hamvocke.com/blog/a-quick-and-easy-guide-to-tmux/)  
tmux를 실행시키고 명령어를 입력하는 것을 ctrl+b를 먼저 살짝 눌렀다 떼면 된다.  
? : help 화면  
% : 좌우로 pane나누기  
" : 아래위로 pane나누기  
화살표 : pane 사이를 이동하기  
z : 현재 pane을 full size로 키우기. 한번더 누르면 원래 사이즈로  
ctrl+d : pane 닫기  
c : 윈도우 하나 더 띄우기  
p, n : 전, 후 윈도우로 이동  
숫자 : 숫자에 해당하는 윈도우로 이동 
, : 현재 윈도우 이름 바꾸기  
d : tmux를 끄지 않고 나가기(detach)  
```bash
tmux ls
tmux attach -t 0
```
명령어로 tmux 화면으로 다시 돌아갈 수 있음.

### ~~GUI실행~~ 구리니까 쓰지말자.
spyder나 pycharm같은 GUI를 실행시킬려면 맥에서 XQuartz안에서 스크린 공유하는 것처럼 실행시킬 수 있다.  
이걸 위해서는 다음과 같이 입력한다.
```bash
ssh -v -Y username@hostip
```

X11 server를 띄우는 방법인데, 이렇게 하려면 다음과 같은 선행조건을 만족시켜야한다.  

__ᅟServer side__
* xauth가 있어야함(xauth info로 확인)
* /etc/ssh/sshd_config 파일에서
```bash
X11Forwarding yes
X11DisplayOffset 10
X11UseLocalhost no
```
가 되어있어야한다.  


__Client side__
* /etc/ssh/ssh_config file에서
```bash
Host *
  ForwardAgent yes
  ForwardX11 yes
```
라고 되어있어야한다.  
하지만 XQuartz가 구리므로 그냥 terminal로만 돌리자.  클라이언트 config에서 위 사항을 주석 처리 하면 -v -Y를 붙이지 않고 접속할 수 있다.
