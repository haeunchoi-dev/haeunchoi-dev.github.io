---
layout: post
title: "원티드 프리온보딩 프론트엔드 인턴십 (3)"
tags: "wanted-frontend-internship"
slug: "wanted-pre-onboarding-frontend-internship-3"
categories: "Study"
---

## 2주차 과제

2주차부터는 기업과제가 주어졌다. 기업의 과제는 주어진 데이터를 활용해 여행 상품 리스트를 조회하고 장바구니에 저장할 수 있는 사이트를 구현하는 것이었다.  
<br>
지난 과제는 사전에 제출한 과제에서 Best Practice를 도출하면 되지만 이번엔 과제를 먼저 수행해야하는 차이점이 있었다. 하지만 제출기한은 저번과 같이 5일이었다. 이전처럼 회의시간에 자신의 코드를 설명하고 살펴보기엔 시간이 촉박했다. 그래서 우린 깃허브의 코드리뷰와 이슈를 이용하기로 했다. 각 요구 사항을 issue로 작성한 뒤 커밋과 연동하여, PR을 생성했을 때 추적을 쉽게 할 수 있도록 하였다. 또 슬랙과 깃허브 레포지토리를 연동하여, PR이 생성될 시 바로 확인할 수 있게 설정을 했다.  
<br>
우리는 2일동안 각자 과제를 수행하고 pr에 이슈별로 코드를 설명하였다. 그리고 다음 회의 전까지 팀원의 코드와 커밋메시지를 참고하여 코드리뷰를 남겼다. 이런 과정을 통해 회의전까지 논의할 내용을 정리할 수 있었고, Best Practice를 이전보다 쉽게 선정할 수 있었다.

### 커밋 메세지 예시

```
feat[#1]: 기능추가
```

이번 과제에선 필수 기술 스택으로 vite와 [Chakra-UI](https://chakra-ui.com/)사용해야 했다. 그 외에 언어나 라이브러리는 협의를 통해 지정할 수 있었다. 언어로는 TypeScript를 사용하고 라이브러리는 React-Query, Redux-Toolkit을 사용하기로 했다. 이번엔 아이콘도 사용해야 했기에, 아이콘 사이트까지 지정했다.
<br>
TypeScript도 사용해본적없고, 다른 라이브러리 또한 사용한적이 없어서 걱정이 됬다. 그래도 최대한 리덕스 툴킷의 [기술 철학](https://ko.redux.js.org/understanding/thinking-in-redux/three-principles)을 지키려고 노력했다.
<br>
리덕스의 3가지 원칙 중 하나인 리듀서를 순수 함수로 제작하는데, 문제가 생겼다.  
장바구니에 저장한 값을 새로고침을 해도 저장할 수 있도록 sessionStorage를 사용하고 싶었는데, 리덕스 툴킷과 상호작용하려 할때 어디서 호출해야 할지 알 수 없었다.  
처음엔 redux-thunk 미들웨어를 이용하려 했다.  
하지만 팀원과 약속된 라이브러리가 아니였기 포기하였다.  
대신 redux-thunk를 공부하면서 알게된 thunk를 이용하기로 했다.

> thunk란 특정 작업을 나중에 하도록 미루기 위해서 함수형태로 감싼것을 칭한다.

```javascript
import { CART } from "../constant/cartConstant";
import { cartActions, type ICartItem } from "./cartSlice";
export const addSaveCartData = (newCart) => {
  return (dispatch, getState) => {
    dispatch(cartActions.addItemToCart(newCart));
    const { cart } = getState();
    sessionStorage.setItem(CART, JSON.stringify(cart));
  };
};
```

이렇게 작성한 action thunk 함수를 dispatch에서 호출한 상태로 입력하면 dispatch에 thunk함수의 포인터가 리턴이된다. 이때 redux toolkit에 의해 내부적으로 매개변수에 dispatch와 getstatus가 입력된다.  
<br>
나름 해결을 했다고 생각하며 마무리하려 했는데, 팀원의 더 좋은 코드를 발견했다. redux middleware를 이용하는 것이다. 가독성도 좋고 중복코드도 줄일 수 있었다.

```javascript
//MIDDLEWARE
const sessionStorageMiddleware: Middleware = ({ getState }) => {
  return (next) => (action) => {
    const result = next(action);
    sessionStorage.setItem(CART, JSON.stringify(getState()));
    return result;
  };
};

const reHydrateStore = () => {
  console.log(sessionStorage.getItem(CART));
  if (sessionStorage.getItem(CART)) {
    return JSON.parse(sessionStorage.getItem(CART) as string);
  }
};

const store = configureStore({
  reducer: { cart: cartReducer },
  preloadedState: reHydrateStore(),
  middleware: (getDefaultMiddleware) =>
    getDefaultMiddleware().concat(sessionStorageMiddleware),
});
```

### 목업

기존엔 백엔드와 프론트엔드 모두 내가 했기때문에 mock data가 필요없었는데, 이번기회를 통해 mock data를 사용하는 방법을 알게 되었다.

```
const axiosConfig: AxiosRequestConfig = {
  timeout: 3000,
  baseURL: "/mock/data.json",
};
```

public에 mock data를 넣어두고 위와 같이 url에 mock data가 있는 경로를 적는 방법, json-server를 설치하는 방법, msw를 사용하는 방법 등 다양한 방법을 알 수 있었다.
<br>

## 2주차 강의

선언형과 명령형 프로그래밍에 대한 설명을 들었다.

### 명령형

```javascript
function addOne(arr) {
  let results = [];
  for (let i = 0; i < arr.length; i += 1) {
    results.push(arr[i] + 1);
  }
  return results;
}
```

### 선언형

```javascript
function addOne(arr) {
  return arr.map((i) => i + 1);
}
```

위 코드를 보면, 명령형은 어떤 동작을 할것인지(HOW) 명령으로 표현하듯이, 순서대로 써놓은것이고 선언형은 무엇(WHAT)과 같은지를 설명한다. 선언형에 대해 좀더 말하자면 map이 어떻게 이루어져 있는지 신경쓰지않고, 상태를 변경하는 모든 지점들은 map 메서드 안쪽으로 **추상화**함으로써 "무엇" 이 이루어지기를 원하는지를 설명하는것이다.
결국 핵심은 추상화인듯한데, 추상화를 이해하는건 참 어려운것 같다. 예전에 이해하고 넘어갔어도 다시 보면 새롭다. ~~공부가 부족한 탓이겠지...~~

<br>

## 회고

알려고 하지 않으면 내가 모른다는 것도 모르게 되는것같다.  
이번에 과제를 하면서, 강의를 들으면서 새로이 알게 되는게 많아 좋으면서 부끄러웠다.
계속 노력해야겠다.

<br>

## 최종결과

![image](https://user-images.githubusercontent.com/86880916/224321973-32aa063d-f024-4052-a15c-93f87244d5a3.gif)
_예약 및 모달 기능_
![image](https://user-images.githubusercontent.com/86880916/224322115-b3d492c5-61a4-4cd2-b13f-3081c69eaf30.gif)_가격 및 지역 필터링 기능_
<br/>

[https://github.com/wanted-pre-7/pre-onboarding-9th-2-7](https://github.com/wanted-pre-7/pre-onboarding-9th-2-7)
