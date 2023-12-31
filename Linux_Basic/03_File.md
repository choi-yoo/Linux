# File
## 파일 속성
```ruby
$ ls -l /etc/hosts
-rw-r--r-- 1 root root 218 Sep 9 10:41 /etc/hosts
```
|속성값|의미|
|:-:|:-:|
|-|파일의 종류|
|rw-r--r--|파일에 대한 read/write/execute에 대한 접근 권한 표시|
|1|하드 링크 개수|
|root|파일 소유자의 로그인 ID|
|root|파일 소유자의 그룹 이름|
|218|파일의 크기(byte)|
|Sep 9 10:41|파일이 마지막으로 수정된 날짜|
|/etc/hosts|파일명|

### 파일의 종류
|문자|파일 유형|
|:-:|:-:|
|-|일반(정규)파일|
|d|디렉터리 파일|
|l|심벌릭 링크 파일|
|b|블록 단위로 읽고 쓰는 블록 장치 파일|
|c|섹터 단위로 읽고 쓰는 문자 장치 파일|
|p|파이프 파일(프로세스 간 통신에 사용되는 특수 파일)|
|s|소켓(네트워크 통신에 사용되는 특수 파일)|
- `file`
    - 파일의 종류를 알려주는 명령어
    ```ruby
    $ file /etc/hosts
    /etc/hosts: ASCII text
    ```

### 파일의 접근 권한
||rw-|r--|r--|
|:-:|:-:|:-:|:-:|
|접근권한|파일의 소유자|파일의 그룹|기타 사용자|
- 파일의 소유자가 chmod 명령을 사용하여 변경 가능

### 하드 링크의 개수
- 하드 링크 = 하나의 파일이 여러 개의 파일명을 가질 수 있도록 하는 기능
- Hard Link vs Soft Link(파일 삭제 비교)
    ```ruby
    # Hard Link
    $ cp /etc/hosts .
    $ ln hosts hardlink
    $ ls -l
    total 8
    -rw-r--r-- 2 opensw opensw 221 Oct 12 20:43 hardlink
    -rw-r--r-- 2 opensw opensw 221 Oct 12 20:43 hosts
    # hosts를 rm하는 것은 hardlink에 어떤 영향도 미치지 못함
    $ rm hosts
    $ cat hardlink
    # hardlink(hosts 파일과 동일한 내용을 가진 파일) 파일의 내용 출력
    127.0.0.1 localhost
    127.0.1.1 opensw
    .
    .
    .
    ```
    ```ruby
    # Soft Link
    $ cp /etc/hosts . 
    $ ls -l
    total 4
    -rw-r--r-- 1 opensw opensw 221 Oct 12 20:46 hosts
    $ ln -s hosts softlink
    $ ls -l
    total 4
    -rw-r--r-- 1 opensw opensw 221 Oct 12 20:46 hosts
    lrwxrwxrwx 1 opensw opensw 5 Oct 12 20:46 softlink -> hosts
    # softlink는 hosts(원본파일)가 삭제되더라도 독립적으로 유효
    $ rm hosts
    $ ls -l
    total 0
    lrwxrwxrwx 1 opensw opensw 5 Oct 12 20:46 softlink -> hosts
    # 오류발생. 원본파일이 삭제되어 softlink 무효화
    $ cat softlink
    cat: softlink: No such file or directory
    ```

### 접근 권한의 종류  
|권한|파일인 경우|디렉터리인 경우|  
|:-:|:-:|:-:|  
|read|파일의 읽기, 복사에 대한 권한|ls 명령 사용 가능|  
|write|파일의 수정, 이동, 삭제 권한<br>해당디렉토리에 쓰기 권한 필요|파일의 생성 또는 삭제 가능|  
|execute|파일의 실행 권한<br>쉘스크립트나 실행파일 등|ls 명령의 **옵션** 사용 가능<br><span style="color:red">cd 명령 사용 가능<br>파일을 디렉토리로 이동/복사 가능<span>|
- 파일 복사(파일의 읽기권한 vs 쓰기권한)
    ```ruby
    # 파일의 읽기 권한만 허용
    $ ls -l t_file.txt
    -r-------- 1 opensw opensw 221 Oct 12 20:46 t_file.txt
    $ mkdir t_dir
    # 복사 가능
    $ cp t_file.txt t_dir
    ```
    ```ruby
    # 파일의 쓰기 권한만 허용
    $ ls -l t_file.txt
    --w------- 1 opensw opensw 221 Oct 12 20:46 t_file.txt
    $ mkdir t_dir
    # 복사 불가
    $ cp t_file.txt t_dir
    cp: cannot open 't_file.txt' for reading: Permission denied
    ```
- 디렉토리 쓰기 권한
    ```ruby
    $ ls -l t_file.txt
    -rw------- 1 opensw opensw 221 Oct 12 20:46 t_file.txt
    # 디렉토리의 읽기 권한만 있는 경우,
    $ ls -ld t_dir
    -r-------- 1 opensw opensw 221 Oct 12 20:46 t_dir
    # 파일 복사 불가
    $ cp t_file.txt t_dir
    cp: cannot stat 't_dir/t_file.txt': Permission denied
    $ chmod u-r,u+w t_dir
    # 디렉토리의 쓰기 권한만 있는 경우,
    $ ls -ld t_dir
    --w------- 1 opensw opensw 221 Oct 12 20:46 t_dir
    # 파일 복사 불가
    $ cp t_file.txt t_dir
    cp: cannot stat 't_dir/t_file.txt': Permission denied
    $ chmod u-w,u+r,u+w
    # 디렉토리의 읽기권한, 실행권한 있는 경우
    $ ls -ld t_dir
    -r-x------ 1 opensw opensw 221 Oct 12 20:46 t_dir
    # 파일 복사 불가
    cp: cannot create regular file 't_dir/t_file.txt': Permission denied
    $ chmod u+w,u-r,u+w
    # 디렉토리의 쓰기권한, 실행권한 있는 경우
    $ ls -ld t_dir
    --wx------ 1 opensw opensw 221 Oct 12 20:46 t_dir
    # 파일 복사 가능    
    ```

1. 접근 권한 표기법
    - r(읽기권한), w(쓰기권한), x(실행권한)
    - 각각의 권한에서 권한이 없는 경우 -로 표기
    - 사용자, 그룹, 기타 사용자 별로 세가지 권한을 rwx 세문자를 묶어서 표기
        - <span style="color:red">rw-<span><span style="color:green">r--<span><span style="color:blue">r--<span>

    1. 파일의 소유자
        - 파일 소유자의 로그인 ID
        - root 계정 : 대개 시스템과 관련된 파일의 소유자
        - 일반 파일은 그 파일을 생성한 사용자가 소유자가 됨

    1. 그룹
        - 파일 소유자의 그룹 이름
            - 리눅스에서 사용자는 하나 이상의 그룹에 속함
            - 시스템 관리자가 그룹에 속할 사용자를 결정 
        - 해당 그룹에 속한 사용자에게 권한 부여 $\Rightarrow$ <span style="color: #2D3748; background-color:#fff5b1;">파일 공유 가능<span>
        - `/etc/group`
            - 그룹이 정의된 파일
            - 시스템 관리자만 수정 가능
            - `groups`
                - 사용자가 속한 그룹을 알려주는 명령어
                ```ruby
                # 인자 지정X -> 현재 로그인한 사용자의 그룹 표시
                $ groups
                opensw adm cdrom sudo dip plugdev lpadmin lxd sambashare
                # 인자 지정O -> 지정한 사용자명이 속한 그룹 표시
                $ groups root
                root : root

                ```
1. `chmod`
    - 파일 또는 디렉토리의 접근 권한 변경
    - `chmod [옵션] <권한모드> <파일명 또는 디렉토리명>`
        - 옵션
             - `-R` : 하위 디렉터리까지 변경
        - 권한 모드
            1. 기호모드 : 문자와 기호 이용
                |사용자 카테고리 문자|연산자 기호|접근 권한 문자|
                |:-:|:-:|:-:|
                ||||  

                |구분|문자/기호|의미|
                |:-:|:-:|:-:|
                ||u|파일 소유자|
                |사용자|g|소유자가 속한 그룹|
                |카테고리 문자|o|소유자와 그룹 이외의 기타 사용자|
                ||a|전체 사용자|
                ||+|권한 추가 부여|
                |연산자 기호|-|권한 제거|
                ||=|접근 권한 설정|
                ||r|읽기 권한|
                |접근 권한 문자|w|쓰기 권한|
                ||x|실행 권한|


                ```ruby
                $ ls -l test.txt
                -rw-rwxrw- 1 opensw opensw 0 Dec 27 12:16 test.txt
                # 기타 사용자의 실행권한 추가
                $ chmod o+x test.txt
                $ ls -l test.txt
                -rw-rwxrwx 1 opensw opensw 0 Dec 27 12:16 test.txt
                # 소유자의 읽기권한 제거
                $ chmod u-r test.txt
                $ ls -l test.txt
                --w-rwxrwx 1 opensw opensw 0 Dec 27 12:16 test.txt
                # 그룹의 읽기,쓰기,실행 권한 제거
                $ chmod g-rwx test.txt
                $ ls -l test.txt
                --w----rwx 1 opensw opensw 0 Dec 27 12:16 test.txt
                # 소유자의 읽기권한 추가, 기타 사용자의 실행권한 제거
                $ chmod u+r,o-x
                $ ls -l test.txt
                -rw----rw- 1 opensw opensw 0 Dec 27 12:16 test.txt
                # 소유자와 기타 사용자의 실행권한 추가
                $ chmod uo+x test.txt
                $ ls -l text.txt
                -rwx---rwx 1 opensw opensw 0 Dec 27 12:16 test.txt
                ```
            1. 숫자모드 : 숫자 이용
                |접근 권한|2진수|8진수|
                |:-:|:-:|:-:|
                |rwx|111|7|
                |rw-|110|6|
                |r-x|101|5|
                |r--|100|4|
                |-wx|011|3|
                |-w-|010|2|
                |--x|001|1|
                |---|000|0|
                ```ruby
                $ ls -l test.txt
                -rw-rw-r-- 1 opensw opensw 0 Dec 27 12:34 test.txt
                $ chmod 444 test.txt
                $ ls -l test.txt
                -r--r--r-- 1 opensw opensw 0 Dec 27 12:34 test.txt
                $ chmod 755 test.txt
                $ ls -l test.txt
                -rwxr-xr-x 1 opensw opensw 0 Dec 27 12:34 test.txt
                ```
1. 기본 접근 권한 설정
    - 파일이나 디렉토리를 생성시에 기본으로 적용되는 접근 권한
        ||소유자|그룹|기타 사용자|
        |:-:|:-:|:-:|:-:|
        |일반 파일|rw-|rw-|r--|
        |디렉터리|rwx|rwx|r-x|
    - `umask`
        - 기본 접근 권한 출력 및 변경 명령어
        - 파일이나 디렉터리 생성 시 <span style="color: #2D3748; background-color:#fff5b1;">부여하지 않을 권한 지정<span>
            - umask 진리표
                |요청권한|마스크|부여된 권한|
                |:-:|:-:|:-:|
                |1|1|0|
                |1|0|1|
                |0|1|0|
                |0|0|0|
        - 최대 접근 권한
            1. 일반 파일 : `666`(rw-rw-rw-)
            1. 실행 파일 / 디렉터리 : `777`(rwxrwxrwx)
                ||일반파일|디렉터리|
                |:-:|:-:|:-:|
                |최대권한|rw- rw- rw-|rwxrwxrwx|
                |2진수|110 110 110|111 111 111|
                |마스크값(002)|000 000 010|000 000 010|
                |마스킹 결과|110 110 100|111 111 101|
                ||6   6   4|7  7  5|
                ||rw- rw- r--|rwx rwx r-x|

            ```ruby
            # 마스크 값이 002(-------w-) == 기타 사용자에게 쓰기권한을 부여X
            umask 002
            # 일반파일
            $ touch masktest.txt
            $ ls -l masktest.txt
            -rw-rw-r-- 1 opensw opensw 0 Dec 27 12:49 masktest.txt
            # 디렉터리
            $ mkdir maskdir
            $ ls -ld maskdir
            drwxrwxr-x 2 opensw opensw 4096 Dec 27 12:49 maskdir
            ```
        - `umask [옵션] [마스크값]`
            - 옵션
                - `-S` : 마스크 값을 문자로 보여준다
            ```ruby
            $ umask 077
            # 마스크 값 숫자로 출력
            $ umask
            0077
            # 마스크 값 문자로 출력
            $ umask -S
            u=rwx,g=,o=
            $ touch linux.txt
            $ ls -l linux.txt
            -rw------- 1 opensw opensw 0 Sep 22 13:33 linux.txt
            ```
1. 특수 접근 권한
    - umask 값 출력 시 4자리 숫자 중 `맨 앞자리 숫자`가 특수 접근 권한을 나타냄
        - 맨 앞자리가 `0`일 경우, **일반 접근 권한**
        - 맨 앞자리가 `1`, `2`, `4`일 경우, **특수 접근 권한**
            1. `4`
                - `SetUID` : 특정 **파일 실행** 동안 파일 소유자의 권한이 적용(파일을 실행한 사용자의 권한X)
                    - SetUID가 설정되면 소유자의 실행권한에 `s` 표시
                ```ruby
                $ touch set
                $ chmod 775 set
                $ ls -l set
                -rwxrwxr-x 1 opensw opensw 0 Sep 22 14:28 set
                # set 파일 실행 시 항상 opensw 사용자의 권한으로 실행되도록 변경
                $ chmod 4775 set
                $ ls -l set
                -rwsrwxr-x 1 opensw opensw 0 Sep 22 14:28 set
                ```
                - 예시 : passwd 명령 ~ `/etc/shadow`(계정의 암호 저장 파일)는 root 계정으로만 수정 가능
                    ```ruby
                    $ which passwd
                    /usr/bin/passwd
                    $ ls -l /usr/bin/passwd
                    # setUID 설정되어 있음
                    -rwsr-xr-x 1 root root 68208 May 28 15:37 /usr/bin/passwd
                    ```
            1. `2`
                - `SetGID` : 특정 **파일 실행** 시 일시적으로 소유 그룹의 권한을 얻어 실행
                    - SetGID가 설정되면 소유 그룹의 실행권한에 `s` 표시
                ```ruby
                $ touch set
                $ ls -l set
                -rw-rw-r-- 1 opensw opensw 0 Sep 22 14:48 set
                # setGID 설정
                $ chmod 2755 set
                $ ls -l set
                -rwxr-sr-x 1 opensw opensw 0 Sep 22 14:48 set
                ```
            1. `1`
                - `sticky bit` : 특정 **디렉터리**에는 누구나 파일 생성 가능
                    - 생성한 파일은 생성한 계정으로 소유자가 설정
                        ```ruby
                        # sticky bit가 설정된 디렉터리
                        $ ls -ld /tmp/
                        drwxrwxrwt 16 root root 4096 Sep 22 13:17 /tmp/
                        $ touch /tmp/hello
                        # 해당 디렉터리 내에 파일 생성할 경우, 소유자는 파일을 생성한 계정
                        $ ls -l /tmp/hello
                        -rw-rw-r-- 1 opensw opensw 0 Sep 22 14:59 /tmp/hello
                        ```
                    - `/tmp` 디렉터리가 대표적
                    - sticky bit가 설정되면 기타 사용자 권한에 `t` 표시
                ```ruby
                $ mkdir temp
                # sticky bit 설정
                $ chmod 1755 temp/
                $ ls -ld temp/
                drwxr-xr-t opensw opensw 4096 Sep 22 15:00 temp/
                ```
    - 특수 권한 접근 설정 오류
        - 특수 권한을 설정하는 파일이나 디렉터리는 **모두 실행 권한**이 있어야 함
            - 실행권한 없는 파일에 SetUID, SetGID 설정 $\Rightarrow$ 's'가 아닌 `S` 표시
            - 실행권한 없는 디렉터리에 sticky bit 설정 $\Rightarrow$ 't'가 아닌 `T` 표시
        ```ruby
        $ touch set
        $ chmod 4644 set
        $ ls -l set
        -rwSr--r-- 1 opensw opensw 0 Sep 22 15:11 set
        $ chmod 2744 set
        -rwxr-Sr-- 1 opensw opensw 0 Sep 22 15:11 set
        $ mkdir temp/
        $ chmod 1754 temp/
        $ ls -ld temp
        drwxr-xr-T 2 opensw opensw 4096 Dec 27 15:21 temp
        ```

### 파일의 크기 
- 파일의 크기는 바이트 단위로 표시

### 파일이 마지막으로 수정된 날짜
- 연도가 표시되어 있지 않을 경우 <span style="color: #2D3748; background-color:#fff5b1;">올해<span>를 의미