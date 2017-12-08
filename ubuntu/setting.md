# Ubuntu 16.04 설치

1. 우분투 배포판 다운로드, USB부팅 디스크 제작
1. 윈도우에서 해야함
1. /는 OS booting 전용 영역 package들을 깔걸 생각해서 64G정도 잡음
1. swap 영역은 윈도우의 cache에 해당 32G
1. 나머지 partition은 /home으로 몰아줌(이럼 사용자ᅟdata는 따로 백업가능)

## Ubuntu에 tensorflow(GPU) 돌리기

1. 기본적으로는 [ubuntu에 tensorflow구동](http://ishuca.tistory.com/m/post/entry/Ubuntu-1404-%EC%97%90%EC%84%9C-%EC%95%84%EB%82%98%EC%BD%98%EB%8B%A4%EC%97%90-Tensorflow-%EC%84%A4%EC%B9%98%ED%95%98%EA%B8%B0)
  이걸따라가고,

1. [terryum 블로그](http://terryum.io/ml_practice/2016/05/15/UbuntuSetup/) 도 참고 할것.

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

## R설치

terminal 에서  update용 sourcelist에 CRAN mirror를 등록하기

```bash
deb http://cran.biodisk.org/bin/linux/ubuntu trusty/
```

로 하는게 젤 빠름.(대구-울산)

참고한 [블로그](http://r.fossa.kr/?p=58), [CRAN 공식문서](https://cran.r-project.org/bin/linux/ubuntu/README)

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
sudo apt-get install xfonts-100dpi #helvetica
sudo apt-get install xfonts-75dpi #for some special idraw I like
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
client $ scp .ssh/id_rsa.pub your_id@server_ip:~/Documents/id_rsa.pub
server $ cat ~/Documents/id_rsa.pub >> authorized_keys
```

로 authorized_keys에 추가해야한다.

클라이언트 키에 pass phrase을 입력했을 경우

ssh server에 접속할때마다 rsa key pass phrase를 입력하는 게 귀찮다면

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

## Remote setting하면 보안에 신경써야함.

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
$ jt -t chesterish -f roboto -fs 11 -nf robotosans -nfs 12 -ofs 10 -dfs 10 -tf robotosans -tfs 12 -T
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

## Using tmux

우분투서버에 터미널로 접속한다음. 한 터미널 윈도우에서 멀티 세션을 돌리고 싶을때 쓴다.

참고 [블로그](http://www.hamvocke.com/blog/a-quick-and-easy-guide-to-tmux/)

tmux를 실행시키고 명령어를 입력하는 것을 ctrl+b를 먼저 살짝 눌렀다 떼면 된다.

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

### ~~GUI실행~~ 구리니까 쓰지말자.

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
## 우분투 swap잡기
새로운 서버에 swap없었음. 
```bash 
$ sudo swapon -s # checking swap
```
없다면, 생성해주자.
```bash
$ sudo fallocate -l 32GB /swapfile
$ sudo chmod 600 /swapfile
$ sudo mkswap /swapfile
$ sudo swapon /swapfile
$ sudo swapon -s # checking swap
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

#### 해결
1. [ASUS bios update](https://www.asus.com/Commercial-Servers-Workstations/X99E_WS/HelpDesk_BIOS/) 내껀 ASUS X99E WS임. 업데이트는 USB에 cap 파일을 담아서 재부팅때 BIOS에 들어가서 해줌. 업데이트 하면서 BIOS setting CPU를 performance mode로 바꿈.
1. [nvidia driver update](http://www.nvidia.com/Download/index.aspx)
1. [grub setting 바꾸기](https://www.pugetsystems.com/labs/hpc/Install-Ubuntu-16-04-or-14-04-and-CUDA-8-and-7-5-for-NVIDIA-Pascal-GPU-825/)
```bash
$ sudo vi /etc/default/grub
LINUX_.....="net.ifnames=0 biosdevname=0 pcie_aspm=off" # in editor deleted "quiet splash"
```

## rsync bakcup

시스템 전부를 백업할때는 /(root directory) 중 몇군데는 빼야한다. 무한루프에 빠질 수 있기때문에.

```bash
sudo -H rsync -aAXv --exclude={/dev/*,/proc/*,/sys/*,/tmp/*,/run/*,/mnt/*,/media/*,/lost+found,/data1/*,/data2/*,/data3/*} / /data3/backup
```

데스크탑에서 서버로 작업한 걸 옮길때도 rsync를 쓰는데, ssh로 작업한다.

```bash
rsync -avzhe ssh ~/Documents/ miruware@192.168.0.11:~/Documents/
rsync -avzhe ssh /media/hyunsu/data\ HDD/01.Data\&Analysis/ miruware@192.168.0.11:/data1/DataAnalysis/
```
