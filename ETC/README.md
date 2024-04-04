## index

- [Terraform](#terraform)

## Terraform

인프라 관리를 코드로 관리할 수 있게 만들어주는 도구다. HCL이라는 언어를 사용해서 클라우드 서비스를 관리한다. 리소스를 정의하고, 커맨드라인을 통해 해당 리소스를 정의할 수 있는데, plan → apply 형태로 관리한다고한다.

**scope**

내 프로젝트 인프라를 식별

**author**

인프라에 대한 구성을 작성 `terraform init` 으로 구성하면 공급자를 다운로드하고 설치한다.

**Initialize**

테라폼 플러그인을 설치 `terraform init` 을 사용한다.

**plan**

내가 설정한 구성을 확인할 수 있다. `terraform plan` 을 사용한다.

**apply** 

planned 를 적용한다. `terraform apply` 를 사용한다.