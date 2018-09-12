# Git tips

## fork 한 repository 유지

github 홈피에서는 pull request를 나한테 날려서 uptream과 update를 맞출 수 있다.
terminal에서는 다음과 같이.

```bash
git remote -v #git upstream이 설정되어있나 확인, origin에는 내 repo가 되어있을 것이다.
git remote add upstream git://github.com/ORIGINAL-DEV-USERNAME/REPO-YOU-FORKED-FROM.git
git fetch upstream #upstream에서 땡겨오고.
git checkout master #branch가 아닌 master로 가서
git merge upstream/master #머지해줌 pull upstream 도 작동하는 것 같다.
```

이 방법은 내가 local 또는 내 repo에 commit 안했을 경우 쉽게된다(홈피에서 하는 것도 마찬가지)  
하지만 commit했을 경우는 PR해서 conflict를 해소 하던지, merge하던지 해야함.  

### local change를 쉽게 돌리기**  

git reset, revert보다는 checkout을 쓰자

```bash
git status
```

변한 파일 이름을 찾아서

```bash
git checkout FILENAME
```

## Git에 실수로 덩치 큰 파일을 commit했을 경우.

.gitignore에 확실하게 파일은 ignore하도록 해서 커밋한다.  
그러고 cach file을 지운다.  

```bash
git rm -r --cached
git add .
```

이렇게 해도 git history object에는 덩치 큰 파일이 들어가 있다.  
commit history도 손을 대야하는데 여기서부터  
__!!!warning!!!__  
잘못하면 local file까지도 날린다.  
__반드시 백업하고 할것!!__

```bash
git filter-branch -f --index-filter 'git rm --cached --ignore-unmatch */*/*.ckpt.*'
```

난 텐서플로 모델을 지우기 위해 파일네임을 위와 같이 썼다.  
만약 . 이나 *을 잘못쓰면 해당조건에 해당하는 모든 파일이 지워진다. 

```bash
git push -f #GitHub remote도 정리할땐 forced push를 한다.
```

## git branch 삭제하기

실수로 branch를 만들었을 경우

```bash
git branch -d <branch_name>
git push --delete <remote_name> <branch_name>
```
