---
layout: post
title: "원티드 프리온보딩 프론트엔드 인턴십 (1)"
tags: "wanted-frontend-internship"
slug: "wanted-pre-onboarding-frontend-internship-1"
categories: "Study"
---

> 참여기간: 23.02.20 - 23.03.17  
> 과정 : 주마다 2회의 세션 강의와 1개의 기업과제를 팀 단위로 수행  
> 팀원 수 : 8

## 지원 계기

si에서 jsp만 사용하다가 view와 리액트에 대해 알게되어 한번 공부해봐야겠다 생각하고 있었다.  
그런데 우연히 [원티드 프리온보딩 프론트엔드 인턴십](https://www.wanted.co.kr/events/pre_ob_fe_9?utm_source=email&utm_medium=braze_mkt&utm_campaign=learning_pre_ob_fe_9) 광고를 보게 됬다.  
리액트로 기업과제를 수행할수 있고, 게다가 팀과제를 경험할 수 있다니 안할 이유가 없었다.

## 선발 과제

사실 98% 안될거라고 생각했다.  
지원을 할때 과제를 제출했어야했는데 제출기한이 2월 15일 까지였다. 나는 14일에 알게되었는데...  
당시 리액트에 대해 들어만 보고 직접 구현한 적이 없기 때문에 자신이 없었다.  
그래도 일단 한번 해보기로 했다. 유데미 강의를 바로 결제하고 과제에 필요한 부분만 찾아서 빨리 듣고 디자인보다 기능에 집중했다. ~~css에 신경을 못써서 제출해서 더 안될거라고 생각했다~~

당연히 요구사항은 모두 만족시켜야겠지만 더 신경을 쓴 지점이 있다면, 크게 3가지가 있었다.

1. 로그인 여부에 따른 리다이렉트  
   로그인 여부에 따라 todo나 로그인 화면으로 리다이렉트를 해야했다.
   우선 제일 먼저 생각한 방법은 useEffect를 이용한 방법이다.  
    인증이 필요한 컴포넌트 내에 useEffect를 사용해 token을 검사하고 인증결과에 따라 리다이렉트를 시키는 방법이다.  
    그런데 이렇게 하면 컴포넌트마다 공통된 코드가 발생하고 유지보수가 어려울것이라는 생각이 들었다.  
    그래서 두번째로 생각한 방법이 **privateRouter**를 만드는 것이다.

   ```html
   <Routes>
     <Route path="/signup" element="{<AuthRoute" component="{<SignUpPage" />}
     authpath={true} />} />
     <Route path="/signin" element="{<AuthRoute" component="{<SignInPage" />}
     authpath={true} />} />
     <Route path="/todo" element="{<AuthRoute" component="{<TodoPage" />}
     authpath={false} />} /> <Route path="*" element="{<NotFound" />} />
   </Routes>
   ```

   AuthRouter를 생성하였다.
   AuthRouter에서 token 유무에 따라 리다이렉트 하는 방식으로 해결했다.

   ```html
   const AuthRoute = ({ component: Component, authpath: Authpath }) => {
   const token = localStorage.getItem(ACCESS_TOKEN);

   return Authpath ? (
       token ? (
       <Navigate to="/todo" />
       ) : (
       Component
       )
   ) : token ? (
       Component
   ) : (
       <Navigate to="/signin" {...alert("로그인이 필요합니다.")}></Navigate>
   );
   };
   ```

   하지만 AuthRouter를 사용하는 코드와 AuthRouter 내부 코드가 깔끔하다는 생각이 들지않아 마지막까지 고민이 되었다.

2. 로그인 시 로그인 상태 관리  
   로그인 했다는 상태를 어떻게 유지할 것인가?  
   첫번째로 생각한게 리액트 상태관리 라이브러리이다. 하지만 그저 로그인 유무만 판단하기엔 오버스펙이라는 생각이 들었다.  
   두번째로 contextApi를 생각했다. 그런데 해당 방법도 오버스펙이 아닌가? 하는 생각이 들었다.  
   그래서 그냥 api파일을 만들고 localstorage에 token을 저장하였다.

   ```html
   export function signin(userDTO) { return call("/auth/signin", "POST",
   userDTO) .then((response) => response.json().then((json) => { if
   (!response.ok) { return Promise.reject(json); } console.log(json) if
   (json.access_token) { localStorage.setItem(ACCESS_TOKEN, json.access_token);
   window.location.href = "/todo"; } }) ) .catch((error) => {
   //alert(error.message); alert("email과 password를 확인해주세요.") }); }
   export function signout() { localStorage.removeItem(ACCESS_TOKEN);
   window.location.href = "/signin"; }
   ```

   그런데 확장과 유지보수를 생각한다면 contextApi를 선택해야하는지, 아니면 주어진 과제에 맞게 개발하는게 옳은지는 잘 모르겠다.

3. 회원가입 및 로그인 시 유효성 검사  
   리액트는 상태가 변경될때 마다 리렌더링을 한다.  
   유효성 검사를 위해서 로그인 input에 값을 확인해야 하는데, 그러기 위해선 input의 value를 상태로 지정하여 관리해야 한다.  
   그렇게 되면 input에 사용자가 값을 입력할때마다 리렌더링이 되는것이 아닌가? 그건 불필요한 리렌더링이라고 생각했다.  
   그래서 ref를 이용하여 input의 값을 가져와 유효성 검사를 수행 후 login 버튼의 상태를 변경하도록 하여 리렌더링을 줄여보았다.
   ```html
   const enteredInputHandler = (event) => { const enteredEmail =
   emailInputRef.current.value; const enteredPassword =
   passwordInputRef.current.value; if (enteredEmail.indexOf("@") > 0 &&
   enteredPassword.length > 7) { setIsDisabledBtn(false); } else {
   setIsDisabledBtn(true); } };
   ```

리액트를 제대로 알지 못하고 시간이 촉박하여 안될거라 생각하면서도 정말 혹시나 하는 마음으로 15일 제출시간 전까지 붙잡고 있었다.  
그런데 정말 감사하게도 합격을 해서 참가하게 되었다!!!! 좋은 기회를 얻은 만큼 열심히 해야겠다고 생각했다.
