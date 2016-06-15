# Coding 관련 bookmark 정리


[점프투 파이썬](https://wikidocs.net/6#fnref:indentation).  
파이썬에 대한 위키 스타일의 전차책.  
이와 관련된 공부는 [이 파일](https://github.com/HyunsuLee/TIL/blob/master/web(bookmark정리)/jumppython.md)에 정리  

## Ubuntu에 tensorflow(GPU) 돌리기

기본적으로는 
[ubuntu에 tensorflow구동](http://ishuca.tistory.com/m/post/entry/Ubuntu-1404-%EC%97%90%EC%84%9C-%EC%95%84%EB%82%98%EC%BD%98%EB%8B%A4%EC%97%90-Tensorflow-%EC%84%A4%EC%B9%98%ED%95%98%EA%B8%B0)

이걸따라가고, 

[terryum 블로그](http://terryum.io/ml_practice/2016/05/15/UbuntuSetup/)
에서 anaconda를 sudo 안쓰고 설치하는 부분을 따라한다

~~이렇게만 하면 terminal에서는 tensorflow가 구동되지만, pycharm안에서는 안된다. 그래서 conda package형태의 tensorflow를 추가함. conda install -c https://conda.anaconda.org/jjhelmus tensorflow 참고한 [stackoverflow](http://stackoverflow.com/questions/33646541/tensorflow-and-anaconda-on-ubuntu)~~

conda package tensorflow를 설치하는것은 아예 다른 버젼을 설치하는것. 

위에 terryum&경제학 blog를 충실히 따라 가되

pycharm 실행을 terminal 에서 해주면 됨


현재 내 컴 환경은

cd pycharm/bin
bash pycharm.sh

참고한 [블로그](http://yeramee.tistory.com/m/post/1)

Pycharm 을 사용하는 동안 terminal을 닫으면 안됨.

## R설치
terminal 에서  update용 sourcelist에 CRAN mirror를 등록하기

deb http://cran.biodisk.org/bin/linux/ubuntu trusty/

로 하는게 젤 빠름.(대구-울산)

참고한 [블로그](http://r.fossa.kr/?p=58), [CRAN 공식문서](https://cran.r-project.org/bin/linux/ubuntu/README)

## Rstudio로 Git version 관리
github에 내 SSH키를 등록 시켜야함.

https://help.github.com/articles/adding-a-new-ssh-key-to-your-github-account/


