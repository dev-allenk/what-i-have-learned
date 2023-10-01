## devDependency

개발 시에만 사용되는 디펜던시를 나타냄  

`npm install` 명령을 실행 시 devDependency도 설치됨  
현재 열린 프로젝트의 개발을 시작하겠다는 의미이기 때문  
반면, `npm install somePackage` 명령을 실행 시 somePackage의 devDependency는 설치되지 않음  
somePackage를 사용하겠다는 의미이지 개발하겠다는 의미는 아니기 때문  

라이브러리를 제작 시 devDependency를 잘 구분해서 작성하는게 좋음  
라이브러리를 사용하는 사람은 개발에 필요한 디펜던시까지 설치하고 싶지는 않을 것이므로  

사실상 프론트엔드 어플리케이션에는 의미가 없음  
`npm install frontendApp` 으로 설치해서 사용할 일은 없기 때문  


## peerDependency

'나'라는 플러그인이 잘 동작하기 위해서 어떤 버전을 사용해야 하는지를 나타냄  
e.g. 리액트 hook을 사용하는 라이브러리는 리액트 16.8버전이상을 피어디펜던시로 가짐

프론트엔드 어플리케이션에서는 쓸 일 없음  
