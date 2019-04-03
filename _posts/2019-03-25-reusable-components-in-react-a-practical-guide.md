---
layout: post
title:  "Reusable Components in React a Practical Guide"
date:   2019-03-25 10:51:47 +0530
categories: Reactjs, Javascript
img: re-usable-component.jpg
categories: [Reactjs, Javascript]
---
[Source](https://blog.bitsrc.io/reusable-components-in-react-a-practical-guide-ec15a81a4d71)

आम तौर पर Javascript में, जब हम किसी code का फिर से उपयोग  अर्थात (re-use) करना चाहते हैं, तो हम इसे एक Function के रूप में लिखते हैं। **हालांकि, ReactJs में, हम एक component के रूप में अपना code लिखते हैं।** इसके लिए हम  एक class बनाते हैं  और फिर जहाँ हम चाहें वहाँ उस class का instances (उदाहरण ) रखते हैं । 

हालांकि, कुछ मामलों को को छोड़ दें तो  यह काम बहुत आसान है। 

यहाँ मैं  एक साधारण  "re-usable React component" create करने  के तरीके के साथ-साथ   ReactJs में बेहतर components create करने के लिए उपयोगी Tricks भी  आपके साथ share करूँगा । 

##### Project Setup 

सबसे पहले, मैं `create-react-app` command का उपयोग करके एक नया react project creaete करना होगा  जैसे : 

`create-react-app my-app`

##### एक simple, re-usable 'react component' का निर्माण :

एक बार project create करने के बाद  `App.js` फ़ाइल मैं पहले से लिखे  code को  निम्न लिखित code से  बदलना होगा : 

```javascript
import React, {Component} from 'react';
import './App.css';

class App extends Component {
  render() {
    return (
      <div>
        <div className="well"><p>A Generic Hello World text</p></div>
      </div>
    )
  }
}

export default App;
```
Figure(1)

यहां, मैंने एक <div> element रखा है जिसके अंदर कुछ text है। यदि मैं इसे दोहराना (repeatation) चाहूँ, तो मुझे फिर से उसी  <div> element को दोबारा लिखना पड़ेगा । 
इसके बजाय मैं एक variable में <div> element को define कर सकता हूँ और उस variable को सीधे my-app के JSX में call करा  सकता हूँ |
जैसे की :-

```javascript
import React, {Component} from 'react';
import './App.css';

const helloWorld = <div className="well"><p>A Generic Hello World text</p></div>;

class App extends Component {
  render() {
    return {helloWorld};
  }
}

export default App;
```
Figure(2)

यदि हम yarn start(mak) या npm start(window) command चलाते हैं, तो हमारा  application "text" को दोनों cases (Figure(1) and Figure(2)) में एक ही  तरह render करता है। 

यहाँ तक कोई दिक्कत नहीं है , लेकिन मैं अगर दो अलग-अलग texts को render करना चाहूं तो .... ?
( अर्थात variable (helloWorld) का use करते हुए <div> element मे जो inner text "A Generic Hello World text" है  | 
इसको dynamically render करना चाहूँ ताकि मेरा  text अलग-अलग हो और <p> and <div> elements एक प्रकार से container के रूप मैं use आये  |

ऐसा करने के लिए, मैं `helloWorld` variable में , हमें कुछ बदलाव करने पड़ेंगे । 

```javascript
import React, {Component} from 'react';
import './App.css';

const message = props => <div className="well"><p>{props.msg}</p></div>;

class App extends Component {
  render() {
    return (
      <div>
        {message({msg: 'A Generic Hello World Text'})}
        {message({msg: 'Creating Reusable React Components'})}
      </div>
    );
  }
}

export default App;
```

सबसे पहले, मैंने variable "helloWorld" का नाम  बदल कर "message" कर दिया है । फिर मैं इस variable के लिए `props` एक argument के रूप में  pass करूँगा । ध्यान दें कि मैं ऐसा करने के लिए ES6 के  arrow Function का उपयोग कर रहा हूं। 

यहाँ  `props` एक  object है जिसमें  'msg' एक property है। `App` compnent के अंदर, मैं {helloWorld} को {message} को भी बदल दिया है  (replace) । इस 'message' variable के अंदर, मैं कुछ text के साथ  'msg' `props` को defining कर रहा हूं । 

इस प्रकार  एक simple, re-usable React component बन गया है। मैंने यहाँ एक function `message`  में  कुछ "JSX" लिखी है, और text को  render करने के लिए एक function `message` को  call किया है। 

लेकिन  वास्तव मैं , इस तरह  यदि किसी Function की calling की जाए, तो   Function भी JSX की तरह  rendering नहीं करता है। इसलिए  हमें Function `message` को ही  JSX में बनाना  होगा। ( यहाँ "message" एक function है  ) इसलिए हम आसानी सा समझ ने के लिए React.createElement के साथ शुरु करेंगे । 
**Note:** यहाँ  हम जिस element को render करना चाहते हैं  उसके  लिए React.createElement में  string के साथ-साथ  Function भी ले सकते हैं। 

```javascript
import React, {Component} from 'react';
import './App.css';

const message = props => <div>{props.msg}</div>;

class App extends Component {
  render() {
    return (
      <div>
        {React.createElement(message, {msg: 'A Generic Hello World Text'})}
        {React.createElement(message, {
          msg: 'Creating Reusable React Components',
        })}
      </div>
    );
  }
}

export default App;
```

यह Function को `props` pass करेगा, और  Function संभवतः कुछ और `element` return करेगा । यहां, `message` Function एक <div className="well"> return कर रहा है। लेकिन यह बहुत जटिल (Complex) दिखाई दे रहा  है। इसे थोड़ा ओर आसान  बनाया जा सकता है जैसे :- 

```javascript
import React, {Component} from 'react';
import './App.css';

const Message = props => <div>{props.msg}</div>;

class App extends Component {
  render() {
    return (
      <div>
        <Message msg="A Generic Hello World Text" />
        <Message msg="Creating Reusable React Components" />
      </div>
    );
  }
}

export default App;
```

यहां, मैं अपने `message` Function को `Message` में बदल रहा हूं, और इस Function को एक component में बदल रहा हूं। ऐसा करके, मैं इसे app component के अंदर एक self-closing element के रूप में उपयोग कर सकता हूं जैसा कि उपरोक्त code में दिखाया गया है। 

यदि मैं `msg` prop को `children` prop में बदल दूंगा, तो भी ReactJs उसी तरह render करेगा जैसा पहले कर रहा था । मैं एक `Message` element को दूसरे `Message` के अंदर भी रख सकता हूं जैसे :- 

```javascript
class App extends Component {
  render() {
    return (
      <Message>
        Hello World
        <Message>Reusable Components</Message>
      </Message>
    );
  }
}
```

तो इस प्रकार , reactJs में एक re-usable component बनाने के लिए, हम बस एक ऐसा function बनाते हैं , जिसमें पहला अक्षर एक कैपिटल लेटर (Capital Letter ) हो। इस Function के  `props` को एक argument के रूप में प्राप्त (get) करते हैं  और हम इन `props` का उपयोग वहां कर सकते हैं जहाँ  जगह हम  अपने re-usable component से  return चाहते हैं। 

#####  Functional components का उपयोग  :
यदि आपके component का एकमात्र उद्देश्य केवल कुछ डेटा प्रदर्शित करना है, तो मैं आपको उस component को Class component के बजाय Functional component के रूप में लिखने का सुझाव दूंगा। 

निम्नलिखित तरीके से  एक  Class component को एक Functional component में बदला जा सकता है: 

**उदाहरण : Class Component**

```javascript
class Hero extends React.Component {
  render() {
    return (
      <div>This could've been a functional component</div>
    )
  }
}
```

**उदाहरण : Converted Functional Component **

```javascript
const Hero = () => <div>Functional Components are so simple!</div>
```

ये दोनों snippets (small code blocks) एक ही "Hero" component को परिभाषित कर रहे हैं। फर्क सिर्फ इतना है कि दूसरा तरीका छोटा और सरल है। ध्यान रहे , Functional components का उपयोग करते समय , हम JSX template मैं  किसी  `props` का  उपयोग करते समय  `this` keyword का  उपयोग करना भूल भी सकते हैं  । 

सुनिश्चित करें कि हम कुछ खास स्थितियां में ही Functional components का उपयोग करना  हैं। ऐसी स्थिति तब होती है जब आपका component केवल कुछ data ही प्रदर्शित कर रहा होता है। 

### Fragments
मान लीजिये की, Reactjs के में  component के अंदर single parent element के लिए यह  आवश्यकता है। 

```javascript
class Login extends React.Component {
  render() {
    return (
        <input name="login"/>
        <input name="password"/>
    )
  }
}
```
यह एक error है। इस समस्या को हल करने के लिए, हम एक simple <div> element को इस तरह से पहले कोड में जोड़ सकते हैं:

```javascript
class Login extends React.Component {
  render() {
    return (
      <div>
        <input name="login"/>
        <input name="password"/>
      </div>
    )
  }
}
```

लेकिन अब, React 16 में  हमें एक नया syntax प्रदान किया गया है जिसे **Fragments** कहा जाता है। Fragments से हम  एक फालतू की  parent `<div>` element के स्थान पर  `<React.Fragment>` का  use कर सकते  हैं।

```javascript
class Login extends React.Component {
  render() {
    return (
      <React.Fragment>
        <input name="login"/>
        <input name="password"/>
      </React.Fragment>
    )
  }
}
```
लेकिन इसे <div> लिखने से भी बुरा माना जा सकता है। इसलिए React ने हमें Fragment के साथ साथ  इसका  short hand syntax भी प्रदान किया है।

```javascript
class Login extends React.Component {
  render() {
    return (
      <>
        <input name="login"/>
        <input name="password"/>
      </>
    )
  }
}
```

### State
state किसी भी React Application में एक महत्वपूर्ण भूमिका निभाता है, और इसलिए यह बहुत महत्वपूर्ण है कि Developers जब वे इसके साथ काम कर रहे हों तो सावधान रहें ।

शुरुआत में state को लिखना  काफी आसान है, हम Class के `render` के अंदर एक `constructor` method बनाते हैं और इसके अंदर state को initialize करते हैं।

फिर, जब हम  Application की  state को update करना चाहते हैं, तो आप `setState` method का उपयोग करते हैं।

setState method मैं  हमें  `this.state` और  `this.props` का उपयोग  करने से बचने की आवश्यकता होती है | 

```javascript
this.setState({ name: this.state.name, name});
```

इसके बजाय  हमें  state को  यथावत  (पहले की तरह ही) रखनी चाहिए , और हमारे state में केवल new `name` property को और जोड़ देंगे ।

```javascript
this.setState({...this.state, name});
```

क्या आपने कभी सोचा कि, यह इतना महत्वपूर्ण क्यों  है? क्योंकि `setState` default रूप से  Asynchronous है, क्योंकि  reactjs स्वयं   application की  state के update होते ही  DOM भी  update करता  है ।

### Difference between Synchronous and Asynchronous: 

जब कोई Calling Method किसी Method को Call करता है और उस Method के पूरी तरह से Execute हो जाने का Wait करता है तथा जब वह Method पूरी तरह से Execute हो जाता है, तब वह Calling Method आगे बढता है, तो इस प्रकार की Programming को Synchronous Programming कहते हैं और इस प्रकार के सभी Methods को Synchronous Method कहते हैं।

किसी भी Program का ये Default Behavior होता है और Multi-Threading के इस Chapter के अलावा हमने पिछले Chapter तक जितने भी Programs Create किए हैं, वे सभी Synchronous Program के Examples हैं।

लेकिन जब कोई Calling Method जिस Method को Call करता है, उस Method के पूरी तरह से Completely Execute होने का Wait नहीं करताए बल्कि Method को Call करके अपने स्वयं के अगले Codes को Process करने लगता है, तो इस प्रकार के Program को Asynchronous Program कहते हैं और इस तरह का Method, जिसके Complete होने के लिए Calling Method Wait नहीं करताए Asynchronous Method कहलाता है।

यानी Asynchronous Method अपना काम पूरा करने से पहले ही Program Control को Calling Method में Return कर देता है ताकि Calling Method के अन्‍य Statements का Execution हो सके।

### Controlled/Uncontrolled Components
ReactJs में, HTML form element अन्य DOM elements से थोड़ा अलग  तरीके से  काम करता है| क्योंकि अन्य DOM elements के विपरीत, form element कुछ internal state भी  रखते हैं। उदाहरण के लिए, साधारण  HTML में निम्न लिखित   form एक ही name स्वीकार (accept) करता है :

```html
<form>
  <label>
    Name:
    <input type="text" name="name" />
  </label>
  <input type="submit" value="Submit" />
</form>
```

जब उपयोगकर्ता (user) form को submit करता है तो इस form में नए page पर जाने का  डिफ़ॉल्ट HTML Form व्यवहार (behavior) होता है। या यों कहें की submit करते ही by default `new page` पर जाता है | यदि आप इस व्यवहार को ReactJs में चाहते हैं, तो यह काम करता है। लेकिन ज्यादातर मामलों में,  इसके लिए एक javascript function का उपयोग  सुविधाजनक रहता है जो form के  submttion को संभालता है और  user द्वारा  Form में दर्ज किये गए  data तक पहुंच (access) प्राप्त करता है। इसे कार्य  करने का stendard तरीका  एक तकनीक के साथ  है। जिसे  `Controlled Components` नामक  है।

Controlled Components का विपरीत , Uncontrolled component कहलाता है! Uncontrolled component एक ऐसी विधि (method) है जहां Form Data को स्वयं DOM द्वारा नियंत्रित किया जाता है।

###  Controlled Components
सामान्यतया HTML में <input>, <textarea>, और <select> जैसे element अपनी state को maintain रखते हैं और अपनी state को  user के input के आधार पर update करते रहते हैं। React में, mutable ( परिवर्तनीय ) state को सामान्यतया components की state property में रखा जाता है, और जिसे केवल setState() से ही update किया जाता है।

हम React में दोनों ( एक जो पहले से state property में था, दूसरा जो user ने  input किया है ) को आपस में जोड़ सकते हैं। ऐसा करते समय React component जो की form को render करता है ओर साथ ही साथ user के input के बाद form में जो changes हुए हैं उनको भी नियंत्रित (control) करता है। एक input Form element जिसका मान ReactJs द्वारा उपरोक्त तरीके से control किया जाता है, उसे “control component” कहा जाता है।

सीधे शब्दों में कहे तो, जिस input(control) की value component की  state से update होती है| वह component "control component" होता है|

```javascript
class NameForm extends React.Component {
  constructor(props) {
    super(props);
    this.state = {value: ''};

    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleChange(event) {
    this.setState({value: event.target.value});
  }

  handleSubmit(event) {
    alert('A name was submitted: ' + this.state.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Name:
          <input type="text" value={this.state.value} onChange={this.handleChange} />
        </label>
        <input type="submit" value="Submit" />
      </form>
    );
  }
}
```

#### Uncontrolled Components
एक अनियंत्रित component लिखने के लिए, हर state के update के लिए एक इवेंट हैंडलर लिखने के बजाय, आप DOM से Form वैल्यू पाने के लिए रेफरी का उपयोग कर सकते हैं।

```javascript
class NameForm extends React.Component {
  constructor(props) {
    super(props);
    this.handleSubmit = this.handleSubmit.bind(this);
    this.input = React.createRef();
  }

  handleSubmit(event) {
    alert('A name was submitted: ' + this.input.current.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Name:
          <input type="text" ref={this.input} />
        </label>
        <input type="submit" value="Submit" />
      </form>
    );
  }
}
```

--- 
#### निष्कर्ष (Conclusion)
जैसा कि मैंने पहले कहा था, ReactJs Component पुन: प्रयोज्य code लिखने का एक शानदार तरीका है। हम बस एक वर्ग बना सकते हैं और फिर जहां चाहें, बस इसके उदाहरणों को कॉल कर सकते हैं। फिर भी, कुछ मामले हैं जो डेवलपर्स को बेहतर विकल्प की तलाश में छोड़ सकते हैं। इनमें से कुछ मामले हैं:

1. यह पूरी तरह से आपका code नहीं है - डेवलपर्स शायद ही कभी अकेले काम करते हैं। वे समूहों में काम करते हैं, गीथहब और बिट जैसे प्लेटफार्मों पर साझा करते हैं। तो, पुन: प्रयोज्य component जिसे आप अपने ऐप में उपयोग कर रहे हैं, शायद स्वयं द्वारा नहीं लिखा जा सकता है। और वह आपको इसके साथ छेड़छाड़ करने से रोक देगा, क्योंकि आप कई बग उत्पन्न करना नहीं चाहते हैं जो आपको ठीक करने में अधिक समय लगेगा।
2. यहाँ, लेकिन वहाँ नहीं - आप पुन: प्रयोज्य component के कुछ व्यवहारों के बारे में चयनात्मक होना चाहते हैं। मतलब, आप इसे कुछ जगहों पर केवल कुछ व्यवहारों के साथ प्रस्तुत करना चाहते हैं
