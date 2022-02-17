---
title: React 如何處理表單元素｜React - The Complete Guide｜bacnotes備份筆記
date: 2022-02-18 00:23:33
updated: 2022-02-18 00:23:33
excerpt: 控制表單輸入有什麼困難的？除了使用者輸入時，減少畫面不必要的渲染，何時觸發驗證，讓錯誤訊息出現的時機剛剛好。使用Custom Hook複用邏輯在多個表單input，也會讓你的程式碼更好讀。
categories:
  - React
---

## 前端驗證時機

1. 當使用者 submit
   等使用者打完再跳出錯誤訊息，避免不必要的開頭就報錯的不好體驗
   沒辦法在輸入完成就告訴使用者有無錯誤，要等到最後，回饋不及時
2. 當 input lose focus
   等使用者打完再跳出錯誤訊息，避免不必要的開頭就報錯的不好體驗
   適合未被點擊過狀態的 form
3. 每個 keystroke
   在輸入資料過程中會出現不必要的錯誤訊息，但可以立即回饋

## 獲取當前表單輸入值

### useState

- 使用 useState 定義初始值跟 set 新值的函式名稱
- 觸發事件，使用 event.target.value 捕捉 input 值
- 適合需要立即驗證、即時捕捉每個 keystroke 的 input

### useRef

- 使用 useRef 綁定指定 DOM input 元素，存在一個變數(通常會命名 xxxRef)
- 觸發事件，透過綁定的變數.current.value 獲取值
- input 初始值可以透過綁定的變數名.current 獲得
- 適合不用獲取每個 keystroke 輸入的 input

```jsx
import { useRef, useState } from "react";

const SimpleInput = (props) => {
  const nameInputRef = useRef();
  const [enteredName, setEnteredName] = useState("");

  const nameInputChangeHandler = (event) => {
    setEnteredName(event.target.value);
  };

  const formSubmissionHandler = (event) => {
    event.preventDefault();
    console.log(enteredName);

    const enteredValue = nameInputRef.current.value;
    console.log(enteredValue);

    // nameInputRef.current.value = ''; => NOT IDEAL, DON'T MANIPULATE THE DOM
    setEnteredName("");
  };

  return (
    <form onSubmit={formSubmissionHandler}>
      <div className='form-control'>
        <label htmlFor='name'>Your Name</label>
        <input
          ref={nameInputRef}
          type='text'
          id='name'
          onChange={nameInputChangeHandler}
          value={enteredName}
        />
      </div>
      <div className='form-actions'>
        <button>Submit</button>
      </div>
    </form>
  );
};

export default SimpleInput;
```

### submit 搭配 keystroke 更新表單值驗證

- 驗證內容為確認表單內容不為空白 e.g.trim()完後的結果不為空字串，或 if(!變數.trim())
- 回饋使用者驗證結果，在 input 欄位下方加上 p 的欄位，條件渲染顯示 e.g.使用 nameInputIsInvalid && <p>some err msg</p>，加上 invalid 樣式
- 為了不要一開始就呈現出 err msg，多加上一個狀態有無 touched 過判斷
- 新增變數作為錯誤訊息渲染判斷(nameInputIsInvalid)，當不符合驗證且有 touched 過才會顯示，!enteredNameIsValid && enteredNameTouched;
- 下方範例為 useState 獲取表單值

```jsx
import { useState } from "react";

const SimpleInput = (props) => {
  // 有新的enteredName就會驗證一次是否空白，re-evaluate(重跑下方函式)
  const [enteredName, setEnteredName] = useState("");
  const [enteredNameTouched, setEnteredNameTouched] = useState(false);
  // 驗證是否為空白
  const enteredNameIsValid = enteredName.trim() !== "";
  const nameInputIsInvalid = !enteredNameIsValid && enteredNameTouched;

  const nameInputChangeHandler = (event) => {
    setEnteredName(event.target.value);
  };

  const formSubmissionHandler = (event) => {
    event.preventDefault();
    // 提交後touched狀態改true
    setEnteredNameTouched(true);

    // 沒過驗證
    if (!enteredNameIsValid) {
      return;
    }

    // 過了驗證，還原表格
    setEnteredName("");
    setEnteredNameTouched(false);
  };

  const nameInputClasses = nameInputIsInvalid
    ? "form-control invalid"
    : "form-control";

  return (
    <form onSubmit={formSubmissionHandler}>
      <div className={nameInputClasses}>
        <label htmlFor='name'>Your Name</label>
        <input
          type='text'
          id='name'
          onChange={nameInputChangeHandler}
          value={enteredName}
        />
        {nameInputIsInvalid && (
          <p className='error-text'>Name must not be empty.</p>
        )}
      </div>
      <div className='form-actions'>
        <button>Submit</button>
      </div>
    </form>
  );
};

export default SimpleInput;
```

### 提早在 input lose focus(blur) 時觸發 touched

- 條件渲染錯誤訊息的兩個條件：驗證沒過且 touched 為真
- 除了 submit 有一個 touched 更新為 true，新增一個 blur 監聽更新 touched 狀態
- 當 input blur 事件觸發，更新 touched 狀態為 true

```jsx
import { useState } from 'react';

const SimpleInput = (props) => {
  const [enteredName, setEnteredName] = useState("");
  const [enteredNameTouched, setEnteredNameTouched] = useState(false);

  const enteredNameIsValid = enteredName.trim() !== '';
  const nameInputIsInvalid = !enteredNameIsValid && enteredNameTouched;

  const nameInputChangeHandler = (event) => {
    setEnteredName(event.target.value);
  };

  // 新增這段 blur即touched過
  const nameInputBlurHandler = event => {
    setEnteredNameTouched(true);
  };

  ...

  return (
    <form onSubmit={formSubmissionHandler}>
      <div className={nameInputClasses}>
        <label htmlFor='name'>Your Name</label>
        <input
          type='text'
          id='name'
          onChange={nameInputChangeHandler}
          // blur監聽
          onBlur={nameInputBlurHandler}
          value={enteredName}
        />
        {nameInputIsInvalid && (
          <p className='error-text'>Name must not be empty.</p>
        )}
      </div>
      <div className='form-actions'>
        <button>Submit</button>
      </div>
    </form>
  );
};

export default SimpleInput;
```

### 使用 Custom Hook 管理多個元素驗證

- 套用Hook在

```jsx
import { useState } from "react";

const SimpleInput = (props) => {
  const [enteredName, setEnteredName] = useState("");
  const [enteredNameTouched, setEnteredNameTouched] = useState(false);

  const [enteredEmail, setEnteredEmail] = useState("");
  const [enteredEmailTouched, setEnteredEmailTouched] = useState(false);

  const enteredNameIsValid = enteredName.trim() !== "";
  const nameInputIsInvalid = !enteredNameIsValid && enteredNameTouched;

  const enteredEmailIsValid = enteredEmail.includes("@");
  const enteredEmailIsInvalid = !enteredEmailIsValid && enteredEmailTouched;

  let formIsValid = false;
  // 兩個驗證都過 整個表單變成valid，submit按鈕可以點
  if (enteredNameIsValid && enteredEmailIsValid) {
    formIsValid = true;
  }

  const nameInputChangeHandler = (event) => {
    setEnteredName(event.target.value);
  };

  const emailInputChangeHandler = (event) => {
    setEnteredEmail(event.target.value);
  };

  const nameInputBlurHandler = (event) => {
    setEnteredNameTouched(true);
  };

  const emailInputBlurHandler = (event) => {
    setEnteredEmailTouched(true);
  };

  const formSubmissionHandler = (event) => {
    event.preventDefault();

    setEnteredNameTouched(true);

    if (!enteredNameIsValid) {
      return;
    }

    console.log(enteredName);

    // nameInputRef.current.value = ''; => NOT IDEAL, DON'T MANIPULATE THE DOM
    setEnteredName("");
    setEnteredNameTouched(false);

    setEnteredEmail("");
    setEnteredEmailTouched(false);
  };

  const nameInputClasses = nameInputIsInvalid
    ? "form-control invalid"
    : "form-control";

  const emailInputClasses = enteredEmailIsInvalid
    ? "form-control invalid"
    : "form-control";

  return (
    <form onSubmit={formSubmissionHandler}>
      <div className={nameInputClasses}>
        <label htmlFor='name'>Your Name</label>
        <input
          type='text'
          id='name'
          onChange={nameInputChangeHandler}
          onBlur={nameInputBlurHandler}
          value={enteredName}
        />
        {nameInputIsInvalid && (
          <p className='error-text'>Name must not be empty.</p>
        )}
      </div>
      <div className={emailInputClasses}>
        <label htmlFor='email'>Your E-Mail</label>
        <input
          type='email'
          id='email'
          onChange={emailInputChangeHandler}
          onBlur={emailInputBlurHandler}
          value={enteredEmail}
        />
        {enteredEmailIsInvalid && (
          <p className='error-text'>Please enter a valid email.</p>
        )}
      </div>
      <div className='form-actions'>
        <button disabled={!formIsValid}>Submit</button>
      </div>
    </form>
  );
};

export default SimpleInput;
```
