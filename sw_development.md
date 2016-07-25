# EDISON 시뮬레이션 SW 개발

EDISON 시스템과 연동을 위해서는 몇 가지 지켜주어야 할 사항이 있다.

### 개발 가능 언어

C++, fortran, java, python 등등 다양한 언어를 지원하며, mpi, openmp등의 병렬처리 라이브러리도 지원하고 있다. 

### 실행 방식 설정 

- 시뮬레이션 실행 방식은 **./[실행파일 명] [옵션] [인풋 파일]** 형태로 실행 되도록 코드를 작성해야 한다. 
      ex) ./run.x –inp data.inp

 - 인풋 파일이 2개 이상 필요한 SW의 경우는 아래와 같이 실행 되도록 코드를 작성해아 한다.     
   **./[실행파일 명] [옵션1] [인풋 파일1] [옵션2] [인풋 파일2] ... **

        ex)  ./run.x –inp data.inp -mesh mesh.msh 
 
#### data1, data2 2개의 변수를 가지는 인풋 파일 1개를 받는 C 예제 코드
 
- input.dat 파일
```
data1 = 10 ;
data2 = 5 ;
```

- main.c 소스 코드

```C
#include <stdio.h>
#include "sub1.h"
#include "sub2.h"

int main(int argc, const char *argv[])
{
        int data1, data2;
        FILE *fp_in, *fp_out;

        if(argv[1] !="-inp")			//옵션 확인 후 입력파일 리드
                fp_in = fopen(argv[2], "rt");
        else
                exit(1);

        fscanf(fp_in, "%*s %*s %d %*s", &data1); //인풋파라미터에서 변수 값 리드
        fscanf(fp_in, "%*s %*s %d %*s", &data2);

        myprint_sub1();
        myprint_sub2();

        system("rm –rf ./result"); 		//결과파일을 저장하기 위한 폴더 생성
        system("mkdir ./result");

        fp_out=fopen("./result/result.dat","wt");	//결과 파일 생성

        fprintf(fp_out,"result = %d", data1+data2);	//결과 파일 저장

        fclose(fp_in);
        fclose(fp_out);

        return 0;
}
``` 

- 컴파일 후 ```$ ./a.out -inp input.dat ``` 로 실행시 정상적으로 동작함을 확인 할 수 있다.  

- **[옵션]**은 꼭 설정 해주어야 한다.
- 소스코드에서 **[인풋 파일]** 처리를 위해 파일 경로 버퍼에 저장하는 경우, 버퍼 공간을 512byte 정도로 넉넉하게 잡아주어야 한다. 
 

### 결과 파일 저장
- 결과 파일은 실행파일 디렉토리에 **result** 폴더를 생성하여 그 안에 저장되어야 한다.  
- 위에 예제에서 처럼 system 명령어를 이용해 result 폴더를 생성하고, 결과 파일을 쓰면된다.

```C
        system("rm –rf ./result"); 		//결과파일을 저장하기 위한 폴더 생성
        system("mkdir ./result");
        fp_out=fopen("./result/result.dat","wt");	//결과 파일 생성
        fprintf(fp_out,"result = %d", data1+data2);	//결과 파일 저장
        fclose(fp_out);

```
