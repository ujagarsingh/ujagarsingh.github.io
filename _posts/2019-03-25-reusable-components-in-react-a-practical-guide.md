---
layout: post
title:  "Reusable Components in React a Practical Guide"
date:   2019-03-25 10:51:47 +0530
categories: jekyll update
img: image-1.png
categories: [one, two]
---

[Original Article](https://blog.bitsrc.io/reusable-components-in-react-a-practical-guide-ec15a81a4d71)

आम तौर पर vanilla Javascript में, जब हम किसी code का फिर से उपयोग (re-use) करना चाहते हैं, तो हम इसे एक Function के रूप में लिखते हैं। *हालांकि, ReactJs में, हम एक component के रूप में अपना code लिखते हैं।* इसके लिए हमें  बस एक class बनाना होगा और फिर जहाँ हम चाहते हैं उस class के कई उदाहरण (instances) रख सकते हैं । 

हालांकि, कुछ मामलों को छोड़कर यह काम बहुत आसान है। 

यहाँ हम एक साधारण  "re-usable React component" बनाना सीखेंगे । और  ReactJs में बेहतर components create करने के लिए उपयोगी सुझावों  पर भी ध्यान दिया जायेगा  । 

##### Project Setup 
सबसे पहले, मैं `create-react-app` command का उपयोग करके एक नया react project creaete करता हूँ : 
'create-react-app my-app`

##### एक simple, re-usable 'react component' का निर्माण :
एक बार project create करने के बाद  `App.js` फ़ाइल पर जाएं और निम्न लिखित code से मौजूदा code को बदलें: 

```javascript
import React, {Component} from 'react';
import './App.css';

class App extends Component {
  render() {
    return (
      <div>
        <div>A Generic Hello World text</div>
      </div>
    )
  }
}

export default App;
```
यहां, एक <div> element है जिसमें इसके अंदर कुछ text है। यदि मैं इसे दोहराना (repeatation) चाहता हूं, तो मुझे फिर से उसी div एलिमेंट को दोबारा लिखना होगा। इसके बजाय हम एक variable के अंदर div element को define कर सकते हैं और उस variable को सीधे my-app के JSX में लिख सकते हैं |
जैसे की -
```javascript
import React, {Component} from 'react';
import './App.css';

const helloWorld = <div>A Generic Hello World Text</div>;

class App extends Component {
  render() {
    return {helloWorld};
  }
}

export default App;
```
यदि हम yarn start(mak) या npm start(window) command चलाते हैं, तो हम देखेंगे कि app "text" को दोनों cases में उसी तरह render करता है। 

क्या होगा यदि मैं किसी अन्य प्रकार के text को render करना चाहूं? या अगर मैं दो अलग-अलग texts को प्रस्तुत करना चाहूं? 

ऐसा करने के लिए, मैं `helloWorld` variable में कुछ बदलाव करने जा रहा हूं। 
```javascript
import React, {Component} from 'react';
import './App.css';

const message = props => <div>{props.msg}</div>;

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

`props` एक  object है जिसमें  'msg' एक property है। `App` compnent के अंदर, मैं {helloWorld} को {message} के साथ बदल रहा (replace) हूं। इस 'message' variable के अंदर, मैं कुछ text के साथ  'msg' props को defining कर रहा हूं। 

अब मैंने एक simple, re-usable React component बना लिया है। एक function के अंदर मैंने कुछ JSX लिए हैं, और कुछ 'text' render करने के लिए एक function call किया है। 

लेकिन Function कॉल वास्तव में और साथ ही JSX भी नहीं करता है। इन Function कॉल को JSX में बदलना बेहतर होगा। इसलिए React.createElement के साथ शुरुआत करें और देखें कि हम JSX में कैसे परिवर्तित हो सकते हैं। React.createElement के बारे में एक बड़ी बात यह है कि हम किस तत्व को प्रस्तुत करना चाहते हैं यह इंगित करने के लिए एक स्ट्रिंग और एक Function ले सकते हैं। 
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
यह Function को props पास करेगा, और वह Function संभवतः कुछ और तत्व देगा। यहां, संदेश Function एक <div> प्रदान कर रहा है। लेकिन यह बहुत जटिल लगता है। इस तरह से चीजों को सरल बनाएं: 
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
यहां, मैं अपने संदेश Function को संदेश में बदल रहा हूं, Function को एक component में बदल रहा हूं। ऐसा करके, मैं इसे एप component के अंदर एक आत्म-समापन तत्व के रूप में उपयोग कर सकता हूं जैसा कि उपरोक्त code स्निपेट में दिखाया गया है। 
यदि मैं msg प्रोप को बच्चों के प्रोप में बदलता हूं, तो ReactJs अभी भी उसी चीज को प्रस्तुत करेगा। मैं एक संदेश तत्व को दूसरे संदेश के अंदर भी रख सकता हूं। 
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
तो, प्रतिक्रिया में एक पुन: प्रयोज्य component बनाने के लिए, आपको बस एक ऐसा कार्य करना होगा, जिसमें पहला अक्षर के रूप में एक कैपिटल लेटर हो। इस Function को प्रॉप्स को एक तर्क के रूप में प्राप्त करना चाहिए और हम इन प्रॉप्स का उपयोग इसमें कर सकते हैं जो कुछ भी यह है कि हम अपने पुन: प्रयोज्य component को वापस करना चाहते हैं। 

##### कार्यात्मक componentों का उपयोग करना 
यदि आपके component का एकमात्र उद्देश्य केवल कुछ डेटा प्रदर्शित करना है, तो मैं आपको उस component को वर्ग component के बजाय कार्यात्मक component के रूप में लिखने का सुझाव दूंगा। 

यहां बताया गया है कि एक विशिष्ट वर्ग component को एक कार्यात्मक component में कैसे बदला जा सकता है: 
```javascript
class Hero extends React.Component {
  render() {
    return (
      <div>This could've been a functional component</div>
    )
  }
}
```
एक कार्यात्मक एक में component, हमें मिलता है: 
```javascript
const Hero = () => <div>Functional Components are so simple!</div>
```
ये दोनों स्निपेट एक ही हीरो component को परिभाषित कर रहे हैं। फर्क सिर्फ इतना है कि दूसरा तरीका छोटा और सरल है। कार्यात्मक componentों का उपयोग करके, हम JSX टेम्पलेट के अंदर किसी भी प्रॉपर तक पहुंचने के लिए इसका उपयोग करने के बारे में भी भूल सकते हैं। 

सुनिश्चित करें कि हम केवल कुछ स्थितियों में कार्यात्मक componentों का उपयोग करते हैं। ऐसी स्थिति तब होती है जब आपका component केवल कुछ डेटा प्रदर्शित कर रहा होता है। 

##### Fragments
टुकड़े प्रतिक्रिया component के अंदर एक एकल मूल तत्व के लिए यह कष्टप्रद आवश्यकता है। 
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
But now, React 16 has provided us with a new syntax called Fragments. Fragments allow us to replace the useless parent div element with a React.Fragment.
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
But this can be considered to be worse than writing div. So React has provided us with this short hand syntax for Fragment as well.
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

##### State
state किसी भी प्रतिक्रिया एप्लिकेशन में एक महत्वपूर्ण भूमिका निभाता है, और इसलिए यह बहुत महत्वपूर्ण है कि डेवलपर्स सावधान रहें जब वे इसके साथ काम कर रहे हों।

लेखन state का प्रारंभिक चरण काफी सरल है, हम कक्षा के रेंडर के अंदर एक कंस्ट्रक्टर विधि बनाते हैं और इसके अंदर state को इनिशियलाइज़ करते हैं।

फिर, जब आप एप्लिकेशन को state को update करना चाहते हैं, तो आप सेटस्ट्रेट विधि का उपयोग करते हैं।

एक चीज जो हमें करने से बचने की आवश्यकता है वह इस का उपयोग कर रहा है। यह इस तरह से सेटस्ट्रेट विधि के अंदर होता है:

```javascript
this.setState({ name: this.state.name, name});
```
इसके बजाय हम जो कर सकते हैं वही state पहले की तरह ही रखें, और हमारे state में केवल नई नाम संपत्ति जोड़ें।

```javascript
this.setState({...this.state, name});
```
यह क्यों इतना महत्वपूर्ण है? क्योंकि सेटस्टैट प्रकृति में अतुल्यकालिक है, क्योंकि हमारे state के update DOM जोड़तोड़ को अनुकूलित करने के लिए बैचबद्ध हैं।

##### Controlled/Uncontrolled Components
प्रतिक्रिया में, HTML फॉर्म एलिमेंट अन्य DOM एलिमेंट्स से थोड़ा अलग काम करते हैं। ऐसा इसलिए है क्योंकि अन्य DOM तत्वों के विपरीत, फार्म तत्व कुछ आंतरिक स्थिति रखते हैं। उदाहरण के लिए, सादे HTML में यह फ़ॉर्म एक ही नाम स्वीकार करता है:
```javascript
<form>
  <label>
    Name:
    <input type="text" name="name" />
  </label>
  <input type="submit" value="Submit" />
</form>
```
जब उपयोगकर्ता प्रपत्र को सबमिट करता है तो इस फ़ॉर्म में नए पृष्ठ पर नेविगेट करने का डिफ़ॉल्ट HTML फॉर्म व्यवहार होता है। यदि आप इस व्यवहार को प्रतिक्रिया में चाहते हैं, तो यह काम करता है। लेकिन ज्यादातर मामलों में, जावास्क्रिप्ट फ़ंक्शन के लिए सुविधाजनक है जो फ़ॉर्म को प्रस्तुत करने के लिए संभालता है और उस डेटा तक पहुंच प्राप्त करता है जो उपयोगकर्ता फॉर्म में दर्ज करता है। इसे प्राप्त करने का मानक तरीका "नियंत्रित componentों" नामक एक तकनीक के साथ है।

नियंत्रित componentों का विकल्प, अनियंत्रित component कहलाता है! अनियंत्रित component एक ऐसी विधि है जहां फॉर्म डेटा को स्वयं डोम द्वारा नियंत्रित किया जाता है।

####  Controlled Components
HTML में, `<input>`, `<textarea>`, और `<select>` जैसे तत्व आमतौर पर अपनी स्थिति बनाए रखते हैं और इसे उपयोगकर्ता इनपुट के आधार पर update करते हैं। प्रतिक्रिया में, उत्परिवर्तनीय स्थिति को आमतौर पर components की state संपत्ति में रखा जाता है, और केवल setState() के साथ update किया जाता है।

हम ReactJs state को "सत्य का एकल स्रोत" बनाकर दोनों को जोड़ सकते हैं। फिर ReactJs component जो किसी प्रपत्र को प्रस्तुत करता है, यह भी नियंत्रित करता है कि बाद के उपयोगकर्ता इनपुट पर उस रूप में क्या होता है। एक इनपुट फॉर्म एलिमेंट जिसका मान ReactJs द्वारा इस तरह नियंत्रित किया जाता है, उसे "नियंत्रित component" कहा जाता है।

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
एक अनियंत्रित component लिखने के लिए, हर state के update के लिए एक इवेंट हैंडलर लिखने के बजाय, आप DOM से फॉर्म वैल्यू पाने के लिए रेफरी का उपयोग कर सकते हैं।

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
