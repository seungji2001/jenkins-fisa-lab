## bind 마운트 & jenkins 배포 과정 자동화 하기 
## root 계정으로 jenkins 실행 후 쓰기 권한 변경
```bash
docker exec -u root -it myjenkins bash
```
### 바인드 마운트
```bash
docker run --name myjenkins2 --privileged -p 8090:8080 -v $(pwd)/appjardir:/var/jenkins_home/appjar jenkins/jenkins:lts-jdk17
```
- **v $(pwd)/appjardir:/var/jenkins_home/appjar**: 이 옵션은 바인드 마운트(bind mount) 옵션입니다.
- `$(pwd)/appjardir`: 현재 작업 디렉토리에서 `appjardir` 폴더를 컨테이너와 공유합니다.
- `/var/jenkins_home/appjar`: Jenkins 컨테이너 내부에서 해당 폴더가 `/var/jenkins_home/appjar`로 마운트됩니다.
이렇게 하면 로컬의 `appjardir` 폴더에 저장된 파일이 컨테이너 내에서 접근 가능해집니다.

### 젠킨스 파이프라인 설정
```bash
pipeline {
    agent any

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/seungji2001/jenkins-fisa-lab.git'
            }
        }
        stage('Build') {
            steps {
                dir('./') {                   
                    sh 'chmod +x gradlew'                    
                    sh './gradlew clean build -x test'
                    sh 'echo $WORKSPACE'
                }
            }
        }
        stage('Copy JAR') {  // 큰따옴표 오류 수정
            steps {
                script {
                def jarFile = 'build/libs/SpringApp-0.0.1-SNAPSHOT.jar'                   
                sh "cp ${jarFile} /var/jenkins_home/appjar/"
                }
            }
        }
    }
}
```

### 젠킨스 루트 계정에 젠킨스 통해 업로드한 git 확인 가능
![image](https://github.com/user-attachments/assets/cd29b7b6-f672-47d6-a123-32d152c5e28e)

### 젠킨스 경로 안에서 배포
![image](https://github.com/user-attachments/assets/780cecff-5a56-4abf-a80e-485bfcdaedda)

### 트러블 슈팅
바인드 마운트 호스트 디렉터리/var/jenkins_home/appjar권한 부여 필요
```bash
chmod +R 755
```
