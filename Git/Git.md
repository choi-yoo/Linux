# TIL
-   당일 학습한 내용 정리

## 2023-03-27
-   git bash 설치
    - `git config --list` : 기본 정보 읽어들임
    - `git config --global user.name "Name"` : 사용자 이름 전역으로 설정
    - `git config --global user.email "E-mail"` : 사용자 이메일 전역으로 설정
    - `git config --unset user.name` `git config --unset user.email` : 사용자 삭제
        - global로 설정된 사용자는 삭제할 때에도 global 옵션 추가
            - ex. `git config --unset --global user.name`
    - **제어판 > 사용자 계정 > 자격 증명 관리 > Windows 자격 증명 > github 항목 삭제 후 수정**
-   기초 리눅스 명령어
-   vim 사용법

## 2023-03-28
-   commit
    -   개념
    -   커밋을 언제 만드는지
    -   커밋 크기
-   저장소
    -   개념
    -   일반 폴더와 저장소의 차이(`.git` 폴더 유무)
    -   `git init`명령어로 저장소 만들기
        -   master 브랜치 생성

## 2023-03-29
-   로컬 머신에 TIL 저장소 만들기
-   스테이징 영역
    -   `git add` 명령어로 커밋에 포함시키고 싶은 파일만 스테이징 영역에 추가
-   커밋 만들기
    -   `git commit -m`
-   GitHub에서 빈 저장소(원격 저장소) 만들기
-   로컬 머신과 GitHub 연동
    -   `git remote add origin "깃헙 주소"` 원격저장소의 별명을 origin으로 즐겨찾기
    -   `git branch -M main` 브랜치 이름이 master에서 main으로 변경
    -   `git push origin main` 로컬저장소의 main 브랜치의 모든 commit들을 원격저장소로 push

## 2023-05-19
- **clone**
    1. 클론하려는 디렉토리로 이동
        - 만약 이동하려는 디렉토리가 존재하지 않는 경우
            - `mkdir` + 디렉토리명
        - `cd` + 이동하려는 디렉토리명
    1. Git 저장소 클론
        - `git clone` + Git 저장소 URL
- **branch** 변경
    - 변경하려는 브랜치가 이미 로컬 또는 원격 저장소에 존재해야 함
        1. 원격 저장소에 해당 브랜치가 없는 경우
            - 해당 브랜치를 원격 저장소에 `git push`
        1. 타 개발자가 생성한 브랜치인 경우
            - 해당 브랜치를 로컬 저장소로 `git pull`
    1. Git 저장소가 있는 디렉토리로 이동
    1. 현재 브랜치 확인
        - `git branch`
        - 현재 브랜치는 '*'로 표시 ex. `* main`
    1. 브랜치 변경
        - `git checkout` + 변경하려는 브랜치명

- **push**
    1. 변경사항 commit(변경사항 없는 경우 생략)
    1. 브랜치 이동
        - `git checkout` + push할 브랜치명
    1. push
        - `git push origin main`
        - 브랜치(main)에 남겨놓은 코드 변경 이력을 저장소(origin)에 push
    
- **pull** : pull한 버전이 현재 내 버전보다 더 최신 버전일 경우 merge
    1. 현재 작업중인 브랜치로 이동
    1. `git pull orgin main`
    1. `git log --decorate --all --oneline`
        - HEAD와 origin/main가 동일한 위치 가리킴
- **fetch** : 단순히 소스를 가져올 뿐 merge X
    1. `git fetch origin`
    1. `git log --decorate --all --oneline`
        - fetch가 성공했는지 확인
        - origin/main, HEAD가 가리키고 있는 위치 다름
    1. `git diff HEAD origin/main`
        - 변경된 내용 확인

## 2023-10-05
- push한 내용 취소
    1. `git checkout main`
        - commit을 되돌릴 브랜치로 이동
    1. `git log`
        - 되돌릴 commit 확인
        - 취소하려는 commit-hash(일련번호) 확인
    1. `git reset --hard {commit-hash}`
        - commit 되돌림
    1. `git push -f origin main`
        - commit을 로컬 브랜치에서 취소한 후, 원격 저장소에도 반영

# 2023-10-12
- remote repository의 파일 삭제
    1. `git rm example.txt`
        - 삭제할 파일을 로컬 저장소에서 삭제
    1. `git commit -m "Delete example.txt"`
        - 삭제한 파일 커밋
        - 로컬 저장소에 반영하기 위함
    1. 
