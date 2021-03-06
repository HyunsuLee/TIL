# Ubuntu 16.04 설치

1. 우분투 배포판 다운로드, USB부팅 디스크 제작
1. 윈도우에서 해야함
1. /는 OS booting 전용 영역 package들을 깔걸 생각해서 64G정도 잡음
1. swap 영역은 윈도우의 cache에 해당 32G
1. 나머지 partition은 /home으로 몰아줌(이럼 사용자 data는 따로 백업가능)
1. 부팅 usb로 부팅할때부터 nouveau때문에 화면이 안보일 수 있다. 이럴 땐.
    1. bios setting 메세지가 뜬 직후 shift를 누르고 grub화면이 뜨는 걸 확인한다.
    1. grub에서 원하는 ubuntu boot option에서 e를 누른다. 그럼 grub setting 화면이 뜸.
    1. linux로 시작하는 line에 modprobe.blacklist=nouveau 를 추가한다.

## Ubuntu에 tensorflow(GPU) 돌리기

1. 기본적으로는 [ubuntu에 tensorflow구동](http://ishuca.tistory.com/m/post/entry/Ubuntu-1404-%EC%97%90%EC%84%9C-%EC%95%84%EB%82%98%EC%BD%98%EB%8B%A4%EC%97%90-Tensorflow-%EC%84%A4%EC%B9%98%ED%95%98%EA%B8%B0)
  이걸따라가고,

1. [terryum 블로그](http://terryum.io/ml_practice/2016/05/15/UbuntuSetup/) 도 참고 할것.

1. 이건 위의 블로그를 참고할 것. (blacklist nouveau를 /etc/modprobe.d/blacklist.conf 에 추가할것. 안 그럼 nvidia driver가 안깔림.)

1. secure boot option도 diabled해둠. 안그럼 driver installer가 kernel에 덮어쓸 수 없음. [텐서플로 공홈](https://www.tensorflow.org/install/gpu)에서도 secure boot가 과정을 복잡하게 한다고 되어 있음.

1. ubuntu server에서는 linux header를 깔아줘야 하는 듯.

```bash
sudo apt-get install linux-source
sudo apt-get install linux-headers-4.15.55-generic
```

1. [nvidia 공식 홈피](http://www.nvidia.com/download/driverResults.aspx/104284/en-us)에서 정식 그래픽 드라이버를 깔고,
1. CUDA를 깔땐 driver, openGL전부 안깐다. cuda랑 샘플만 깔아줌.

1. [텐서플로우 공식페이지](https://www.tensorflow.org/versions/r0.9/get_started/os_setup.html#anaconda-installation) 를 참고하여 anaconda를 python 2.7버젼과 python 3.5버젼 용으로 따로 깔고, conda 가상 환경을 각각 만들어줌.

1. 마찬가지로 conda 가상 환경 안에서 알맞는 tensorflow version을 각각 Install. (pip version이 안맞는다는 error도 conda깔고 그안에서 이것저것 깔면 해결)

* pycharm 실행을 conda 가상 환경 terminal 에서 해주면 됨
* 현재 내 컴 환경은

```bash
server $ cd pycharm/bin
server $ bash pycharm.sh
```

1. 참고한 [블로그](http://yeramee.tistory.com/m/post/1)
1. Pycharm 을 사용하는 동안 terminal을 닫으면 안됨.

## GPU driver가 사라졌을때

~~!!!요부분은 아직 경험하지 못한 부분 우분투 14.04를 쓸 땐 CUDA driver를 썼지만,
16에서는 따로 드라이버를 잡았으로, 그 드라이버를 새로 깔아서 해결해보자.
그전에 이 현상이 안 일어나야겠지.... 자동 업데하다 nvidia랑 충돌나서 생기는 문제인 거 같은데.~~

* __!!!결론적으로 자동 업데가 화근이었다.__
* __최근 받은 미루웨서 매뉴얼에 따르면 GUI autoupdate를 켜놓으면 dist-upgrade를 자동으로 하는데__
* __그러면 추천 앱+커널이 업데된다고 한다. 커널이 업데되면 임의로 깔은 nvidia driver를 인식 못 함...__
* __따라서 GUI autoupdate를 never로 해두고, 터미널에서 dist-upgrade가 아닌 update -> upgrade만 할것!!__
* __만약 dist-uprade를 해서 그래픽 드라이버를 인식못하면, nvidia driver만 다시 깔면됨__
* __CUDA는 건드리지 말것.__

~~일단 피씨 본체의 reset 버튼으로 강제 리부팅을 하면 날아 가는듯하다~~
~~(모니터 Input source 때문에 몇번 리부팅했을때 괜찮은 걸보면 GPU 컴퓨팅 중에 하면 날아가는듯)~~

## CUDA 여러version 쓰기

### anaconda environment 명령어 활용

* anaconda의 경우 environment를 (de)activation시킬 때마다 script를 자동으로 가동시킬 수 있다.
* conda env name안에 아래와 같은 directory를 만들어주면 됨.
* [참고블로그](https://blog.kovalevskyi.com/multiple-version-of-cuda-libraries-on-the-same-machine-b9502d50ae77)
* [정식conda홈피](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#activating-an-environment) 
* miniconda는 path가 다르므로 주의할 것.

```bash
cd $CONDA_PREFIX
mkdir -p ./etc/conda/activate.d
mkdir -p ./etc/conda/deactivate.d
touch ./etc/conda/activate.d/env_vars.sh
touch ./etc/conda/deactivate.d/env_vars.sh
```

* activate.d안의 sh파일에는 다음과 같이, cuda 9.0을 load하고 싶을 경우

```bash
ORIGINAL_LD_LIBRARY_PATH=$LD_LIBRARY_PATH
export LD_LIBRARY_PATH=/usr/local/cuda-9.0/lib64
```

* cuda 10.0부터는 CUPTI도 load 해주자.

```bash
ORIGINAL_LD_LIBRARY_PATH=$LD_LIBRARY_PATH
export LD_LIBRARY_PATH=/usr/local/cuda-10.0/lib64:/usr/local/cuda-10.0/extras/CUPTI/lib64
```

* deactivate.d안의 sh파일 에는 원래 symbolic link로 돌려주는

```bash
export LD_LIBRARY_PATH=$ORIGINAL_LD_LIBRARY_PATH
unset ORIGINAL_LD_LIBRARY_PATH
```

### 아래와 같은 해결책은 옛날 방식

* CUDA 9.0을 깔고, python3.5, tensorflow1.5 환경을 구축했다.
* 하지만 python2.7, tensorflow1.0을 유지하려면 CUDA8.0을 써야함.
* 참고로 CUDA toolkit 만 깔땐 lighdm stop필요없음. nvidia driver 깔때 필요함.
* ~~nvidia driver 깔때 X server option에서 yes. no를 하면 무한 로그인현상 발생.~~

__CUDA 여러 version을 쓰기위해선 symbolic link를 바꿀 필요가 있다.__
__다음과 같이 바꾼다.__

```bash
sudo rm /usr/local/cuda # 기존 symbolic link 삭제
sudo ln -sT /usr/local/cuda-8.0 /usr/local/cuda # 새로운 symbolic link 형성
```

## CUDNN libray command line에서 깔기

* 아마 home directory ~/cuda에 CUDNN이 생성되어 있을것이다. 이것을 현재 CUDA version에 덮어 준다.
* __주의, 현재 CUDA version을 symbolic link로 가서 확인하라.__
* __아예 적합한 버젼의 CUDA directory로 지정해주는게 나음.__

```bash
sudo cp cuda/include/cudnn.h /usr/local/cuda/include
sudo cp cuda/lib64/libcudnn* /usr/local/cuda/lib64
sudo chmod a+r /usr/local/cuda/include/cudnn.h /usr/local/cuda/lib64/libcudnn*
```

## CUDA가 날아갔을때

1. 재부팅후 ctrl+alt+F1으로 가상터미널 접속
1. sudo stop lightdm, sudo service lightdm stop
1. su sh cuda_7.5.18_linux.run(nvidia driver yes, openGL no)
1. sudo reboot 하면 드라이버는 가동
1. CUDNN다시 카피(sudo nautilus로 관리자 탐색기 띄우고 Home/cuda에 있는 파일들을 usr/local/cuda로 각각 옮긴다.)

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

관리자 탐색기에서 /etc/default/grub  을 backup한다.

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

## 모니터 먹통 현상 해결

### input source 바꾸기 등으로 우분투로 부터의 display가 안뜰때

ctrl + alt + F1 으로 가상터미널로 들어가 display가 뜨는걸 확인하고(~~optional~~ display output을 refresh 필수인듯)

ctrl + alt + F7 으로 GUI환경으로 다시 돌아 온다.

## 우분투 무한 로긴

* 최근 우분투 18.04로 업그레이드하면서 생겼던 문제.
* 업그레이드하면서 당연히 kernel이 업데되면서 그래픽카드 드라이버는 재설정해줘야 했음. 이건 예상했던 문제.
* 그래픽 카드를 재설치하고, 심지어 CUDA에 포함된 그래픽카드로 다시 잡아도 문제가 해결안되었음.
* 다음과 같은 방법으로 (아마) 해결된 듯.
* 그전에 기본으로 깔리는 gdm에서 lightdm으로 바꿔줘야함.

```bash
sudo dpkg-reconfigure lightdm
```

* 물론 내 경우에는 이것으로 해결되진 않았음. [참고싸이트](https://askubuntu.com/questions/223501/ubuntu-gets-stuck-in-a-login-loop)

```bash
ls -lA
-rw-------  1 root root   53 Nov 29 10:19 .Xauthority
# 위와 같이 뜬다면
chown username:username .Xauthority # 내 로긴네임
# 여기에 추가로
ls -ld /tmp
drwxrwxrwt 15 root root 4096 Nov 30 04:17 /tmp
# 앞의 drwx~~~~~ 가 제대로 되어 있는지 확인
sudo chmod a+wt /tmp
```

* 아마도 위의 두가지 해결법(.Xauthority와 tmp) 중에 하나가 먹힌 듯하다.
* 4GPU machine을 업글하지 말고 환경만 유지 할것.

## 우분투, 윈도우10 멀티부팅

* 내 경우에는 우분투 18.04가 있는 상태에서 윈도우10을 추가 파티션에 인스톨했다.
* 그 결과 윈도우 부트 매니저가 활성화되어 원도우로 자동 부팅되었는데, grub option을 살리고 싶었다.
* 만약 윈도우가 깔려있는 상태에서 우분투를 깔면 grub option booting이 자동 설정된다.
* 이런 저런 삽질을 하다, 결국 이 [싸이트](https://silentinfotech.com/blog/steps-to-install-windows-10-on-existing-ubuntu-16-04/)에 나와있는 방법으로 해결했다.
* Live ubuntu가 되는 bootable usb를 준비하고, UEFI모드로 부팅.
* 그 다음에는 boot-repair 를 깔아서 repair 시켜주면, grub이 전반적으로 재설정되면서 윈도우 부팅 옵션도 추가된다. 

## R설치

terminal 에서  update용 sourcelist에 CRAN mirror를 등록하기

```bash
deb http://cran.biodisk.org/bin/linux/ubuntu trusty/ # 16.04는 xenial
```

로 하는게 젤 빠름.(대구-울산)

참고한 [CRAN 공식문서](https://cran.r-project.org/bin/linux/ubuntu/README)

## R package

* 기본적으로는 install.pakages 로 설치한다.
* 만약 R version upgrade후에 기존의 R에서 쓰던 package를 그대로 옮기고 싶다면. ~/R/ folder안에 구버전에 있는 library들을 새 버전 R folder로 옮긴다. 겹치는건 skip.

## Rstudio로 Git version 관리

github에 내 SSH키를 등록 시켜야함.

[GitHub help](https://help.github.com/articles/adding-a-new-ssh-key-to-your-github-account/)

터미널에서 한번 작업을 해줘야함. ~~HTTP가 아닌 SSH방식으로~~

```bash
git config remote.origin.url git@github.com:HyunsuLee/???.git
git pull origin master
git push origin master
```

모든 git repository에 대해 작업 해줘야하는 듯.

~~repository를 clone할때부터 SSH방식을 쓰면 될듯.~~

GitHub은 HTTP를 추천한다.

### Git fork syncing

```bash
git remote -v # upstream repository check.
# 없다면,
# git remote add upstream https://github.com/ORIGINAL_OWNER/ORIGINAL_REPOSITORY.git
git fetch upstream
git checkout master
git merge upstream/master
```

## allensdk

현재 document에 python notebook file들을 받아 test중.

그중 biophysical model은 [NEURON](http://www.neuron.yale.edu./neuron/download/compile_linux#step2)이 있어야 기능함.

iv(interview)라는 것은 먼저 설치해야하며

$HOME에 neuron/iv, neuron 디렉토리에 파일을 두개 풀고 설치함.

./configure 하면 X11이 없다고 오류가 나는데

```bash
sudo apt-get install libxext-dev
sudo apt-get install mercurial bison flex automake libtool g++ rpm
sudo apt-get install libxext-dev libncurses-dev python-dev
sudo apt-get install cython alien
sudo apt-get install xfonts-100dpi # helvetica
sudo apt-get install xfonts-75dpi # for some special idraw I like
```

들을 깔아주면 해결.

~~다 깔고 나서 nrniv나 nrngui는 안먹지만(명령어가 없다고 나옴)~~

Python에 neuron package은 불러와짐. allen conda env에 설치함.

```bash
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

## ubuntu mouse scroll setting

```bash
xinput list 
```

하면 mouse 이름이 뜸.

```bash
device=$(xinput list --id-only 'Logitech USB-PS/2 Optical Mouse')
xinput list-props $device
```

하면 mouse setting 들이 나오는데, 내 경우는

```bash
Evdev Scrolling Distance (284):	-1, 1, 1
```

항목이 있음. 제일 앞의 수가 1이면 윈도우 스타일, -1이면 MAC 스타일  

```bash
xinput set-prop $device "Evdev Scrolling Distance" -1, 1, 1
```

이 setting을 고정하려면 ~/.bashrc 파일에 device= 항목과 set-prop항목을 추가하자.
  

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

```ᅟbash
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

## high resolution display vs Application issue

matlab의 경우, matlab command에서 다음과 같이 입력하면 해결.

```bash
>> s = settings;s.matlab.desktop.DisplayScaleFactor
>> s.matlab.desktop.DisplayScaleFactor.PersonalValue = 2
```

[참고 문서](https://wiki.archlinux.org/index.php/HiDPI#MATLAB)

### Ubuntu 18.04 scaling

* high resolution display를 쓰고 있는데, 18.04에는 100%, 200%단위로만 display scaling 설정이 가능하다.
* 하지만 text scaling만 조정하는 방법이 있다.

```bash
gsettings set org.gnome.desktop.interface text-scaling-factor 1.5
```

## Ubuntu ssh server setting

맥과 우분투 모두 ssh는 기본으로 인스톨 되어있다.

가장 간단하게 서버에 접속하는 방법은 클라이언트에서 다음과 같이 입력하는 거다.

```bash
ssh username@hostip
```

나는 서버의 내 계정과 서버 ip를 바로 사용했지만 서버에서 사용자 list를 관리할 수 있다.

### RSA key 만들고 쓰기

```bash
client $ ssh-keygen -t rsa
```

rsa 키를 만들어주면.

* ~/.ssh/id_rsa : private key
* ~/.ssh/id_rsa.pub : public key

가 만들어진다. (내 맥북에는 이미 있음)

이중 public key를 서버로 카피해야함.

```bash
# client $ scp .ssh/id_rsa.pub your_id@server_ip:.ssh/authorized_keys
# 이렇게 하면 한 대의 클라언트 컴퓨터의 key만 으로 overwrite되어 버림.
client $ ssh-copy-id your_id@server_ip
```

로 하거나

```bash
client $ scp .ssh/id_rsa.pub your_id@server_ip:~/id_rsa.pub
server $ cat ~/id_rsa.pub >> ~/.ssh/authorized_keys
```

로 authorized_keys에 추가해야한다.

클라이언트 키에 pass phrase을 입력했을 경우

ssh server에 접속할때마다 rsa key pass phrase를 입력하는 게 귀찮다면

```bash
client $ ssh-add
     input : passphrase
```

해두면 안 묻더라.

### ssh server remote setting

외부에서도 서버에 접속하기 위해서는 공유기 port forwarding을 쓰면 된다.

공유기 setting에서 서버 ip를 잡고, port를 ~~외부~~, 내부 모두 22(ssh)로 잡아줌.
(대학 전산망이 port 22는 filtering해서 안됨.)

closed port 중에 숫자가 큰걸 외부 port로 잡았음(port ????)

이러면 공유기 ip로 접속된 ssh가 공유기 하의 서버 ip로 넘어감.

```bash
ssh -p ???? your_id@server_ip
```

로 간단하게 외부망에서도 접근할 수 있음.(테스트 통과)

## Remote setting하면 보안에 신경써야함

### sshd config file

etc/ssh/sshd_config에 다음과 같은 세팅을 제대로 해두자.

```bash
Protocol 2 # Protocol 1은 보안이슈가 있다. 보통 기본임.
AllowUsers root vivek jerry #특정 유저만 허용, DenyUsers를 써서 ban하는 방법도.
ClientAliveInterval 300 # 300sec까지만 허용.
ClientAliveCountMax 0
IgnoreRhosts yes #기본으로 되어있음.
HostbasedAuthentication no #기본으로 되어있음.
Banner /etc/issue.net #접속시 경고 배너 띄우기. 난 안 쓴다.
PermitEmptyPasswords no #기본으로 되어있음.
LogLevel INFO #기본으로 되어있음.
PasswordAuthentication no #RSA key를 쓰면 이건 꺼두자.
```

### ssh setting을 바꿔 준 다음에는 ssh server restart해야함

```bash
systemctl restart sshd # ubuntu 16.
sudo service ssh restart # ubuntu 14.
```

### ssh server log 시도 파일 보기

그중에서 실패한 시도들만 따로 보는 방법

```bash
server $ tail /var/log/auth.log -n 100 | grep 'Failed'
```

## jupyter notebook 관련

참고 [블로그](http://www.justinkiggins.com/blog/zero-configuration-remote-jupyter-server/)
서버에서 주피터를 브라우저 없이 띄운다

```bash
server $ jupyter notebook --no-browser --port=8888
```

port 8888로 띄운걸, client에서 ssh로 접속해서 땡겨옴.

```bash
client $ ssh -NL 8888:localhost:8888 your_id@server_ip
```

다음 내 브라우저에서 localhost:8888을 띄우면 server의 주피터가 뜸.

### notebook theme

참고 [GitHub](https://github.com/dunovank/jupyter-themes)

```bash
jt -t chesterish -f roboto -fs 11 -nf robotosans -nfs 12 -ofs 10 -dfs 10 -tf robotosans -tfs 12 -T
```

dark theme(chesterish)에 code font roboto(11), markdown font robotosans(12).
notebook font robotosans(12), output&pandas fontsize(10)으로 설정.

### notebook extension

참고 [GitHub](https://github.com/ipython-contrib/jupyter_contrib_nbextensions)

```bash
#install
$ pip install jupyter_contrib_nbextensions
$ jupyter contrib nbextension install --user #extension들 인스톨
$ pip install jupyter_nbextensions_configurator #extension켜고 끄는 extension 인스톨
$ jupyter nbextensions_configurator enable --user #configurator들 켜기.
```

이렇게 하면 notebook GUI에서 extensions을 찾아 켜고 끌 수 있다.

### jupter lab error

* nodejs error가 뜰 경우 nodejs를 다시 깐다. LTS support version은 현재 10임.
* [source site](https://github.com/nodesource/distributions/blob/master/README.md#debinstall)

```bash
curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash -
sudo apt-get install -y nodejs
```

* 그래도 error가 나는 경우, Failed validating schema (@jupyterlab... 등의 error가 날때, rebuild를 해준다.
* [stack over flow 답](https://stackoverflow.com/questions/57260338/what-is-a-failed-validating-schema-jupyterlab-apputils-extensionpackage-in) 

```bash
jupyter lab clean && jupyter lab build
```

## Using tmux

우분투서버에 터미널로 접속한다음. 한 터미널 윈도우에서 멀티 세션을 돌리고 싶을때 쓴다.

참고 [블로그](http://www.hamvocke.com/blog/a-quick-and-easy-guide-to-tmux/)

tmux를 실행시키고 명령어를 입력하는 것을 ctrl+b를 먼저 살짝 눌렀다 떼면 된다.

현재 tmux prefix는 ctrl+a로 바꿔둠.[참고](http://www.hamvocke.com/blog/a-guide-to-customizing-your-tmux-conf/)

* ? : help 화면
* % : 좌우로 pane나누기
* " : 아래위로 pane나누기
* 화살표 : pane 사이를 이동하기
* z : 현재 pane을 full size로 키우기. 한번더 누르면 원래 사이즈로
* ctrl+d : pane 닫기
* c : 윈도우 하나 더 띄우기
* p, n : 전, 후 윈도우로 이동
* 숫자 : 숫자에 해당하는 윈도우로 이동
* , : 현재 윈도우 이름 바꾸기
* d : tmux를 끄지 않고 나가기(detach)

```bash
tmux ls
tmux attach -t 0
```

명령어로 tmux 화면으로 다시 돌아갈 수 있음.

### ~~GUI실행~~ 구리니까 쓰지말자

spyder나 pycharm같은 GUI를 실행시킬려면 맥에서 XQuartz안에서 스크린 공유하는 것처럼 실행시킬 수 있다.

이걸 위해서는 다음과 같이 입력한다.

```bash
ssh -X username@hostip
```

X11 server를 띄우는 방법인데, 이렇게 하려면 다음과 같은 선행조건을 만족시켜야한다.

#### Server side

* xauth가 있어야함(xauth info로 확인)
* /etc/ssh/sshd_config 파일에서

```bash
X11Forwarding yes
X11DisplayOffset 10
X11UseLocalhost no
```

가 되어있어야한다.

#### Client side

* /etc/ssh/ssh_config file에서

```bash
Host *
  ForwardAgent yes
  ForwardX11 yes
```

라고 되어있어야한다.
하지만 XQuartz가 구리므로 그냥 terminal로만 돌리자. 클라이언트 config에서 위 사항을 주석 처리 하면 -v -Y를 붙이지 않고 접속할 수 있다.

### __GUI를 우분투끼리 실행시키면 쓸만함.__

## 새로온 우분투 서버관련

python3 path가 잘못잡혀있었다. root 계정과 miruware 계정이 다른 python path를 잡고 있었음.

```bash
$ vim ~/.bashrc
export PATH = "/usr/local/lib/python2.7/dist-packages$PATH"
export PATH = "/usr/local/lib/python3.4/dist-packages$PATH"
```

리부팅 후 network setting이 안 잡히면 ifconfig로 확인 후 eth0이나 eth1이 있는지 확인. 없으면

```bash
sudo ifconfig eth0 up
sudo dhclient eth0
```

## 우분투 swap잡기

새로운 서버에 swap없었음.

```bash
$ sudo swapon -s # checking swap
Filename    Type  Size  Used  Priority
```

없다면, 생성해주자.

```bash
$ sudo fallocate -l 32GB /swapfile
$ sudo chmod 600 /swapfile
$ sudo mkswap /swapfile
$ sudo swapon /swapfile
$ sudo swapon -s # checking swap
Filename    Type  Size  Used  Priority
/swapfile  file  31249996 0    -1
```

부팅시에도 잡아줘야하므로

```bash
$ sudo vi /etc/sftab
/swapfile   none swap   0    0  # in editor
$ sudo vi /etc/sysctl.conf
vm.swappiness = 10 # in editor
$ sudo sysctl -p
```

## tensorflow hanging during computation

cifar10_multi_gpu tutorial test중에 연산하다가 자꾸 멈추는 현상이 지속됨
[비슷한 보고](https://github.com/tensorflow/tensorflow/issues/1947), [또 다른 보고](https://github.com/tensorflow/tensorflow/issues/8696) 등이 있음. 종합해본 결과 ASUS motherboard의 PCIe와 nvidia card 가 서로 궁합이 안맞는 것으로 추정.

### 해결

1. [ASUS bios update](https://www.asus.com/Commercial-Servers-Workstations/X99E_WS/HelpDesk_BIOS/) 내껀 ASUS X99E WS임. 업데이트는 USB에 cap 파일을 담아서 재부팅때 BIOS에 들어가서 해줌. 업데이트 하면서 BIOS setting CPU를 performance mode로 바꿈.
1. [nvidia driver update](http://www.nvidia.com/Download/index.aspx)
1. [grub setting 바꾸기](https://www.pugetsystems.com/labs/hpc/Install-Ubuntu-16-04-or-14-04-and-CUDA-8-and-7-5-for-NVIDIA-Pascal-GPU-825/)

```bash
$ sudo vi /etc/default/grub
LINUX_.....="biosdevname=0 pcie_aspm=off" # in editor deleted "quiet splash"
```

* net.ifnames=0 도 추가했더니, network setting이 이상해짐. 빼고 pcie error는 안 뜸.

## remote server tensorboard 사용하기

* 내 local machine에서

```bash
ssh -N -f -L localhost:16006:localhost:6006 <user@remote>
```

* server에서

```bash
tensorboard --logdir <path>
```

* 내 local machine 브라우저에서 localhost:16006 하면됨.

* ᅟserver에서 locale 설정 오류가 나곤 하는데.

```bash
export LC_ALL=C
```

* 영구적으로 locale 설정을 위해서

```bash
$ sudo vi /etc/default/locale
# insert following line(according to locale error messages)
LC_ALL="en_US.UTF-8"
LANGUAGE="en_US.UTF-8"
```

## rsync bakcup

시스템 전부를 백업할때는 /(root directory) 중 몇군데는 빼야한다. 무한루프에 빠질 수 있기때문에.

```bash
sudo -H rsync -aAXv --progress /home/media/data1/ /home/media/data2/
sudo -H rsync -avAXHS --progress --exclude={/dev/*,/proc/*,/sys/*,/tmp/*,/run/*,/mnt/*,/media/*,/lost+found,/home/media/*} / /home/media/data3/backup
```

데스크탑에서 서버로 작업한 걸 옮길때도 rsync를 쓰는데, ssh로 작업한다. 먼저 대상 폴더에 대한 권한을 설정해줘야 함.

```bash
sudo chmod -R a+rwx <destination folder>
```

```bash
rsync -aAXv --progress --delete -e 'ssh -C -p $port_number' /home/hyunsu/Documents/ <user name>@<ip address>:/home/<user name>/Documents/
rsync -aAXv --progress --delete -e 'ssh -C -p $port_number' /media/hyunsu/data2/01.DataAnalysis/ <user name>@<ip address>:/home/media/data1/DataAnalysis/
```

* bash 실행파일로 만들어둠.

* crontab으로 아예 자동화 할 예정.

* mac OSX에서는 *를 붙여주어야 함. DevonDB를 빼고 싶었으나, 잘 안됨..

```bash
sudo rsync -avzhe /Volumes/Storage/* /Volumes/HDD3/*
```

또한 일부 파일에 대해 permission이 없는듯한데,(이거 때문에 finder에서 복사가 안된듯) 해결방법을 모르겠음.

### crontab으로 backup 자동화, 그 후 아침까지 suspend

* sudo crontab -e 으로 명령어를 넣어두고, 모든 경로는 root 계정이라고 생각하고 절대경로로.
* 내 desktop에서는

```bash
00 23 * * 0-5 /usr/bin/rsync -aAXv --delete -e 'ssh -i /path/.ssh/id_rsa_empty -C -p $port_number' /home/hyunsu/Documents/ <user name>@s<ip address>:/home/hyunsu4gpu/Documents/
20 23 * * 0-5 /usr/bin/rsync -aAXv --delete -e 'ssh -i /path/.ssh/id_rsa_empty -C -p $port_number' /media/hyunsu/data2/01.DataAnalysis/ <user name>@s<ip address>:/home/media/data1/DataAnalysis/ # rsa key를 passwordless로 만들어둬야 실행됨.
50 23 * * 0-4 /usr/sbin/rtcwake -u -s 28800 -m mem # sleep for 8hr
50 23 * * 5 /usr/sbin/rtcwake -u -s 169200 -m mem # sleep for 47hr
```

* server에서는

```bash
50 23 * * 0-5 rsync -aAXv --delete /home/media/data1/ /home/media/data2/
30 00 * * 1-6 rsync -avAXHS --exclude=/dev/* --exclude=/proc/* --exclude=/sys/* --exclude=/tmp/* --exclude=/run/* --exclude=/mnt/* --exclude=/media/* --exclude=/lost+found --exclude=/home/media/* /* /home/media/data3/backup # cron안에서 {}를 쓰면 못 알아먹음. 이렇게 다 따로
50 00 * * 1-5 /usr/sbin/rtcwake -u -s 28800 -m mem # sleep for 8hr
50 00 * * 6 /usr/sbin/rtcwake -u -s 168600 -m mem # sleep for 46hr 50min
```

* bash file로 root backup을 할 경우, exclude 옵션이 적용 안 된것 같아서 이렇게 해둠. 아마 {}안에 있는 것을 다 무시해서 그런듯.
* rsync 명령어는 /usr/bin에 있어서 cron에서도 실행이 되는 것 같지만, (root $PATH를 고려할 것) rtcwake를 시행이 안되서 이것도 절대경로로 처리해둠.

```bash
which rtcwake
```

* 이 명령어로 해당 실행명령의 절대경로를 알 수 있음.

### suspend 상태에서 USB 장치로 wake up시키기

* sudo vi /etc/rc.local 로 파일을 열고, exit 0 위에 다음과 같은 명령어를 추가.

```bash
echo enabled > /sys/bus/usb/devices/1-13/power/wakeup
echo enabled > /sys/bus/usb/devices/1-14/power/wakeup
echo enabled > /sys/bus/usb/devices/1-6.2/power/wakeup
echo enabled > /sys/bus/usb/devices/1-6/power/wakeup
echo enabled > /sys/bus/usb/devices/2-6/power/wakeup
echo enabled > /sys/bus/usb/devices/usb1/power/wakeup
echo enabled > /sys/bus/usb/devices/usb2/power/wakeup
```

* 모든 USB 장치를 wake up enabled 로 만드는 것. 

## 동영상 변환

```bash
ffmpeg
```

[documentation](http://ffmpeg.org/ffmpeg.html#Video-Options)

## 파일명 변환

rename 명령어를 쓴다. 

```bash
rename "s/ /_/g" *
```

모든 파일 * 에 대해 공백(s/ )을 __(/_/g)로 치환.
