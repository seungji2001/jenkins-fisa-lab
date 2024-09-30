# bind 마운트 & jenkins 배포 과정 자동화 하기 
### root 계정으로 jenkins 실행 후 쓰기 권한 변경
```bash
docker exec -u root -it myjenkins bash
```
### 바인드 마운트
```bash
docker run --name myjenkins2 --privileged -p 8090:8080 -v $(pwd)/appjardir:/var/jenkins_home/appjar jenkins/jenkins:lts-jdk17
```
- **v $(pwd)/appjardir:/var/jenkins_home/appjar**: 이 옵션은 바인드 마운트(bind mount) 옵션입니다.
- `$(pwd)/appjardir`: 현재 작업 디렉토리에서 `appjardir` 폴더를 컨테이너와 공유합니다.
- `/var/jenkins_home/appjar`: Jenkins 컨테이너 내부에서 해당 폴더가 `/var/jenkins_home/appjar`로 마운트됩니다. 이렇게 하면 로컬의 `appjardir` 폴더에 저장된 파일이 컨테이너 내에서 접근 가능해집니다.
