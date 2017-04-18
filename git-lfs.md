# [Git Large File Storage](https://git-lfs.github.com/)

git에 업로드 파일 용량 제한이 있는 경우 사용하게 된다.
git-lfs client를 설치한 후,
```
git lfs install
git lfs track "*.psd"
git config --global lfs.url [LFS_URL]
git add .gitattributes
```

실제로 파일은 별도의 저장소에 저장하고 checksum만 commit하는 방식이 된다.
반드시! 대용량 파일을 local git에 commit하기 전에 .gitattributes를 origin에 푸시할 것을 권한다.

lfs url이 repository마다 다를 경우,
* local configuration을 사용하던가.
```
git config --local lfs.url [LFS_URL]
```
* .lfsconfig를 사용해야 한다고.
```
git config -f .lfsconfig lfs.url [LFS_URL]
```

## [Github Tutorial](https://github.com/git-lfs/git-lfs/wiki/Tutorial)
