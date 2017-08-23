## fork 한 repository 유지
github 홈피에서는 pull request를 나한테 날려서 uptream과 update를 맞출 수 있다.   
terminal에서는 다음과 같이.
```
git remote -v #git upstream이 설정되어있나 확인, origin에는 내 repo가 되어있을 것이다.
git remote add upstream git://github.com/ORIGINAL-DEV-USERNAME/REPO-YOU-FORKED-FROM.git
git fetch upstream #upstream에서 땡겨오고.
git checkout master #branch가 아닌 master로 가서
git merge upstream/master #머지해줌
```
이 방법은 내가 local 또는 내 repo에 commit 안했을 경우 쉽게된다(홈피에서 하는 것도 마찬가지)  
하지만 commit했을 경우는 PR해서 conflict를 해소 하던지, merge하던지 해야함.  

**local change를 쉽게 돌리기**  
git reset, revert보다는 checkout을 쓰자
```
git status
```
변한 파일 이름을 찾아서
```
git checkout FILENAME
```
