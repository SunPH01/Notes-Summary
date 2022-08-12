```css
input[type="radio"],input[type="checkbox"]{
  -webkit-appearance: none;
  outline: none;
}
input[type="radio"]{
  position: relative;
  width: .12rem;
  height: .12rem;
  border: 1px solid #186D9A;
  border-radius: .06rem;
  -webkit-border-radius: .06rem;
  -moz-border-radius:.06rem;
  vertical-align: middle;
  background: white;
}

input[type="radio"]:checked:after {
  content: '';
  position: absolute;
  left: .02rem;
  top: .02rem;
  width: .06rem;
  height: .06rem;
  background-color: #186D9A;
  border-radius: .03rem;
  -webkit-border-radius: .03rem;
  -moz-border-radius:.03rem;
}
input[type="radio"]:checked {
  background-color: #fff;
}
input[type="checkbox"] {
  position: relative;
  width: .12rem;
  height: .12rem;
  border: .01rem solid #186D9A;
  border-radius: .02rem;
  vertical-align: middle;
  background: white;
  margin-top: 0;
}
input[type="checkbox"]:checked:after {
  content: '';
  position: absolute;
  left: .02rem;
  top: .02rem;
  width: .07rem;
  height: .04rem;
  border-left: .02rem solid #186D9A;
  border-bottom: .02rem solid #186D9A;
  transform: rotate(-45deg);
}
input[type="checkbox"]:checked {
  background-color: #fff;
}
```