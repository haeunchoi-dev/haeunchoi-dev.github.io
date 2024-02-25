---
layout: post
title: "원티드 프리온보딩 프론트엔드 인턴십 (2)"
tags: "wanted-frontend-internship"
slug: "wanted-pre-onboarding-frontend-internship-2"
categories: "Study"
---

## 1주차 과제

팀을 배정 받고 처음으로 받은 과제는 사전 과제였던 todo 앱의 Best Practice를 선정하는 것이다.

> Best Practice란 팀원들이 각자의 구현방법을 설명하고 토론했을 때 팀 안에서 이 방법이 가장 효율적이라고 판단되는 것

Best Practice를 도출하기 위해 우리가 선택한 방식은 전반적으로 디자인과 코드가 좋은 유지석 팀장님의 코드를 기반으로 각자 새로운 todo앱을 만들어 오는 것이었다.

감사하게도 팀장님이 초기 코드를 올려주었고, 우리는 해당 코드를 기반으로 각자 다른 팀원의 Best Practice를 선정하여 새로운 todo 앱을 만들었다. 그리고 이틀 후 어떤 코드를 참고했는지 이유와 함께 설명하고 최종 제출물을 선정하였다.

내가 Best Practice를 선정할때 주요하게 본 부분은 사전과제를 할때 고민한 부분이었다.

1. **로그인 여부에 따른 리다이렉트**  
   지난주 나는 PrivateRouter와 useEffect 중에서 고민을 하였다. 그런데 팀원의 코드 중 Router를 사용하는 방법이 있었다. 내가 생각하지 못했던 방식을 알게되어 좋았다. 게다가 불필요한 페이지 이동이 없고 코드 가독성도 좋다고 생각했다. (역시 공부는 여러사람과 하는게 좋다.)
   ```
         <Route
           path="/"
           exact
           element={token ? <Navigate to="/todo" replace /> : <SignIn />}
         />
   ```
   팀원과 privateRouter, Router, useEffect 각각의 장단점에 대해 의견을 나누었다. 나는 useEffect를 사용시 유지보수와 중복코드를 걱정하였는데, 컴포넌트가 마운트 된 후 이펙트를 처리하므로 사용자가 화면 깜빡인 현상을 경험할 수 있다는 단점도 알게 되었다.
2. **상태 관리**  
   ContextApi와 reducer를 사용하는 것이 프로젝트 규모에 맞는지에 대한 토론을 하였다. 나는 프로젝트 규모가 작고, props depth가 깊지 않아 props drilling이 일어나지 않기에 Status로 충분해 보인다고 생각했다. 하지만 auth가 전역으로 쓰이기에 ContextApi가 적합하고 reducer를 사용하면 상태를 분리해 재사용성을 높이고 다양한 액션을 가진 todo 상태 간의 관계를 파악하기 쉽다는 의견을 듣고 생각을 바꿨다.
   ```javascript
   const reducer = (state, action) => {
     switch (action.type) {
       case AUTH_ACTION.SET_TOKEN:
         localStorage.setItem(ACCESS_TOKEN, action.token);
         return { ...state, token: action.token };
       case AUTH_ACTION.RESET_TOKEN:
         localStorage.removeItem(ACCESS_TOKEN);
         return { ...state, token: "" };
       default:
         return state;
     }
   };
   ```

## 회고

감사하게도 내가 만든 todo를 최종 팀과제로 제출하게 되었다. ~~팀원들의 Best Practice를 잘 가져온것 같다~~  
팀원들의 코드가 모두 나보다 훌륭하여 배울점이 너무 많았다. 이전에 사전과제를 수행하며 고민했던 부분을 해결할 수 있었다.
<br>
이번 프론트엔드 인턴십은 팀을 구성해주었지만 파트를 분배하고 협업하는 방식이 아니었다. 다만, 각자가 개인 프로젝트를 수행하는데, 다른 사람의 코드를 검토하고 토론하는 기회를 통해 실력을 향상시킬 수 있었다.
<br>
팀 프로젝트를 진행하더라도 업무를 파트로 분배하여 동일한 주제로 코드를 작성하는 기회가 많이 없었다. 하지만 이번 프로젝트에서는 동일한 주제를 다루고 있어서, 다른 팀원들이 어떻게 고민한 부분을 해결했는지 살펴보고 배울 수 있었다. 또한, 고민하고 있는 부분을 팀원들과 함께 토론하며 해결해 나가는 과정이 매우 즐거웠다.

## 최종결과

<img src="https://user-images.githubusercontent.com/80516736/221163889-f6e55521-5a84-402b-bb25-977f35efd3a1.gif">

[https://github.com/wanted-pre-7/wanted-pre-onboarding-frontend](https://github.com/wanted-pre-7/wanted-pre-onboarding-frontend)
