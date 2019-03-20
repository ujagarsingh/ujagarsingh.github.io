---
layout: post
title: Why use Redux or Mobx in Reactjs
img: react-redux-mobx.png
---

https://blog.logrocket.com/why-use-redux-reasons-with-clear-examples-d21bffd5835

यह 2018 है, Software विकास में उपयोग करने के लिए कई नए libraries और tools के साथ एक समय। tool और libraries की संख्या के साथ, (एक Javascript libraries  शायद इसे पढ़ने से पहले जारी किया गया था), यह वास्तव में लाभों को समझने के बिना या आपको इसका उपयोग क्यों करना चाहिए, हर नए पर कूदने के लिए सबसे समझदार बात नहीं हो सकती है।

Redux नया नहीं है और यह काफी लोकप्रिय है। हालाँकि, यदि आपको कोई पता नहीं है कि Redux क्या है और आप जानना चाहते हैं कि आपको इसका उपयोग क्यों करना चाहिए, तो यह लेख आपके लिए है। भले ही आप जानते हों कि Redux क्या है, लेकिन फिर भी आश्चर्य होता है कि क्या आप अपने ऐप में इसका उपयोग करके कोई बुद्धिमान विकल्प बना रहे हैं, यह लेख भी आपके लिए है।

इस लेख में, हम कुछ कारणों पर विचार करेंगे कि आपको इसके लाभों पर चर्चा करके Redux का उपयोग क्यों करना चाहिए। सबसे पहले, हम Redux की मूल बातें समझेंगे और यह कैसे कार्य करेगा। फिर हम देखेंगे कि एक सरल लेकिन practical Component का उपयोग करके Redux कैसे आपके App में मदद कर सकता है।

Redux क्या है?
Redux Javascript अनुप्रयोगों के लिए एक अनुमानित State container है। यह आपको उन applications को लिखने में मदद करता है जो लगातार व्यवहार करते हैं, विभिन्न वातावरणों (Client, Server और Native) में चलते हैं, और परीक्षण करना आसान है। -  https://redux.js.org/

सीधे शब्दों में कहें, Redux एक state management tool है। जबकि इसका उपयोग ज्यादातर React के साथ किया जाता है, इसका उपयोग किसी अन्य Javascript framework या library के साथ किया जा सकता है। यह 2KB पर निर्भर है (निर्भरता सहित), इसलिए आपको इसके बारे में चिंता करने की ज़रूरत नहीं है कि यह आपके application’s की assets का आकार बड़ा कर सकता है।

Redux के साथ, आपके Application की state को एक store में रखा जाता है और प्रत्येक Component किसी भी state को access कर सकता है जिसे उसे इस store से चाहिए। आइए थोड़ा गहराई से देखें कि आपको state management tool की आवश्यकता क्यों हो सकती है।


आपको state management tool की आवश्यकता क्यों है
इस लेख की शुरुआत में, हमने यह स्पष्ट किया कि Redux एक state management tool है लेकिन अब, आइए विचार करें कि आपको state management tool की आवश्यकता क्यों हो सकती है।

अधिकांश libraries जैसे React, Angular, आदि को External libraries या tool की आवश्यकता के बिना Components को आंतरिक रूप से अपने state का प्रबंधन करने के लिए बनाया गया है। यह कुछ Components के साथ Application s के लिए अच्छी तरह से करता है लेकिन जैसे-जैसे Application’s आकार बड़ा होता जाता है, Components के बीच share किए गए प्रबंध States का एक हिस्सा बन जाता है।

एक App में जहां data को Components के बीच share किया जाता है, यह वास्तव में भ्रमित करने वाला हो सकता है कि एक state कहाँ रहना चाहिए। आदर्श रूप में, एक Component में data केवल एक Component में रहना चाहिए। इसलिए sibling Components के बीच data share करना मुश्किल हो जाता है।

उदाहरण के लिए, React में, siblings के बीच data share करने के लिए, एक state को मूल Component में रहना पड़ता है। इस state को अपडेट करने के लिए एक method  इस मूल Component द्वारा प्रदान की जाती है और इन भाई Components को Props रूप में पारित किया जाता है।

यहाँ React में एक Login Component का एक सरल उदाहरण है। Login Component का इनपुट उसके sibling Component, स्टेटस कंपोनेंट द्वारा प्रदर्शित क्या प्रभावित करता है:

class App extends React.Component {
 constructor(props) {
 super(props);
 // First the Parent creates a state for what will be passed
 this.state = { userStatus: "NOT LOGGED IN"}
 this.setStatus = this.setStatus.bind(this);
 }
 // A method is provided for the child component to update the state of the
 // userStatus
 setStatus(username, password) {
 const newUsers = users;
 newUsers.map(user => {
  if (user.username == username && user.password === password) {
  this.setState({
   userStatus: "LOGGED IN"
  })
  }
 });
 }
 
render() {
 return (
  <div>
  // the state is passed to the sibling as a props as is updated whenever
  // the child component changes the input
  <Status status={this.state.userStatus} />
  // this method is passed to the child component as a props which it
  // uses to change the state of the userStatus
  <Login handleSubmit={this.setStatus} />
  </div>
 );
 }
});
💡 याद रखें, इस data को मूल Component की आवश्यकता नहीं है, लेकिन क्योंकि इसके बच्चों को data share करने की आवश्यकता है, इसलिए इसे एक state प्रदान करना होगा।
अब सोचिए कि जब किसी state को उन Components के बीच share करना होता है जो Component पेड़ में बहुत दूर होते हैं। state को एक Component से दूसरे Component में तब तक पारित करना पड़ता है जब तक कि इसकी आवश्यकता न हो।

मूल रूप से, state को निकटतम parent के Component तक और अगले तक उठाया जाएगा जब तक कि यह पूर्वज के लिए नहीं हो जाता है जो कि दोनों Components के लिए सामान्य है जो state की आवश्यकता है और फिर इसे नीचे पारित किया जाता है। इससे state को बनाए रखने में मुश्किल होती है और पूर्वानुमान कम होता है। इसका मतलब उन Components को data पास करना है, जिन्हें ऐसे data की आवश्यकता नहीं है।

यह स्पष्ट है कि App के जटिल होते ही state प्रबंधन गड़बड़ा जाता है। यही कारण है कि आपको Redux जैसे state management tool की आवश्यकता है जो इन States को बनाए रखना आसान बनाता है। आइए इसके लाभों पर विचार करने से पहले Redux अवधारणाओं का एक अच्छा अवलोकन करें।

यह समझना कि Redux कैसे काम करता है
Redux के काम करने का तरीका सरल है। एक केंद्रीय store है जो Application के पूरे state को रखता है। प्रत्येक Component एक Component से दूसरे Component में प्रॉप्स भेजने के बिना संग्रहीत state तक पहुंच सकता है।

तीन भवन भाग हैं: क्रिया , भंडार और पुनर्विकास । आइए संक्षेप में चर्चा करें कि उनमें से प्रत्येक क्या करता है। यह महत्वपूर्ण है क्योंकि वे Redux के लाभों को समझने में आपकी सहायता करते हैं और इसका उपयोग कैसे किया जाता है। हम उपर्युक्त Login Component में एक समान उदाहरण लागू करेंगे लेकिन इस बार Redux में।

Redux में क्रियाएँ
सीधे शब्दों में कहें तो क्रियाएँ घटनाएँ हैं। वे एकमात्र तरीका है जिससे आप अपने Application से अपने Redux store में data भेज सकते हैं। data उपयोगकर्ता के इंटरैक्शन, एपीआई कॉल या यहां तक ​​कि फ़ॉर्म सबमिशन से भी हो सकता है।

क्रियाएँ store.dispatch () पद्धति का उपयोग करके भेजी जाती हैं। क्रियाएँ सादा Javascript Object हैं और उनके पास एक प्रकार की assets होनी चाहिए जिससे कि कार्रवाई के प्रकार को इंगित किया जा सके। उनके पास एक पेलोड भी होना चाहिए जिसमें वह जानकारी हो, जिस पर कार्रवाई की जानी चाहिए। कार्रवाई एक निर्माता के माध्यम से बनाई जाती है।

यहां एक एक्शन का उदाहरण दिया गया है जो किसी App में Login के दौरान किया जा सकता है:

{ 
 type: "LOGIN",
 payload: {
 username: "foo",
 password: "bar"
 }
}
यहाँ इसके एक्शन निर्माता का एक उदाहरण है:

const setLoginStatus = (name, password) => {
 return {
 type: "LOGIN",
 payload: {
 username: "foo",
 password: "bar"
 }
 }
}
जैसा कि पहले बताया गया है, कार्रवाई में टाइप प्रॉपर्टी होनी चाहिए और फिर अन्य पेलोड को संग्रहीत करना होगा।

Redux में Reducers
Reducers शुद्ध कार्य हैं जो किसी Application की वर्तमान state को लेते हैं, एक क्रिया करते हैं और एक नया state लौटाते हैं। इन States को Object के रूप में संग्रहीत किया जाता है और वे निर्दिष्ट करते हैं कि store में भेजे गए एक्शन के जवाब में Application की state कैसे बदलती है।

यह Javascript में कम फ़ंक्शन पर आधारित है जहां एक कॉलबैक फ़ंक्शन किए जाने के बाद एकल मान कई मानों से प्राप्त होता है।

यहाँ कैसे का एक उदाहरण है reducers Redux में काम करते हैं:

const LoginComponent = (state = initialState, action) => {
    switch (action.type) {
    
      // This reducer handles any action with type "LOGIN"
      case "LOGIN":
          return state.map(user => {
              if (user.username !== action.username) {
                  return user;
              }
        
              if (user.password == action.password) {
                  return {
                      ...user,
                      login_status: "LOGGED IN"
                  }
              }
          });
      default:
          return state;
      } 
};

And Reducers App की पिछली state लेते हैं और इसे पारित की गई कार्रवाई के आधार पर एक नया state लौटाते हैं।
शुद्ध कार्यों के रूप में, वे इसमें दिए गए Object में data को नहीं बदलते हैं या Application में कोई साइड इफेक्ट नहीं करते हैं। एक ही वस्तु को देखते हुए, यह हमेशा एक ही परिणाम उत्पन्न करना चाहिए।

Redux में store करें
store Application’s state रखता है। किसी भी Redux Application में केवल एक store है। आप संग्रहीत state तक पहुँच सकते हैं, state को अपडेट कर सकते हैं, और सहायक method यों के माध्यम से श्रोताओं को पंजीकृत या अपंजीकृत कर सकते हैं।

आइए हमारे Login App के लिए एक store बनाएं:

const store = createStore(LoginComponent);
state पर किया गया कार्य हमेशा एक नया state देता है। इस प्रकार, state बहुत आसान और अनुमानित है।

अब जब हम Redux के बारे में कुछ और जानते हैं, तो चलिए अपने Login कंपोनेंट उदाहरण पर वापस चलते हैं जो पहले लागू किया गया था और फिर देखें कि Redux कंपोनेंट को कैसे बेहतर बना सकता है।

class App React.Component { 
 render () { 
 return ( 
  <div> 
  <Status user = {this.props.user.name} /> 
  <login login = {this.props.setLoginStatus} / 
  </ div> 
 ) 
 } का विस्तार करता है 
}
Redux के साथ store में एक सामान्य state है और प्रत्येक Component की state तक पहुंच है। यह state को एक Component से दूसरे Component में लगातार पारित करने की आवश्यकता को समाप्त करता है।

React के साथ Redux का उपयोग करते समय, States को अब उठाने की आवश्यकता नहीं होगी, इसलिए यह आपके लिए यह पता लगाना आसान बनाता है कि कौन सी कार्रवाई आपके परिवर्तन का कारण बनती है। जैसा कि ऊपर देखा गया है, Component को अपने बच्चों के Components को आपस में data share करने के लिए कोई state या method प्रदान करने की आवश्यकता नहीं है। सब कुछ Redux द्वारा नियंत्रित किया जाता है। यह App को बहुत सरल करता है और इसे बनाए रखना आसान बनाता है।

हालांकि यह प्राथमिक लाभ और कारण है कि आपको Redux का उपयोग क्यों करना चाहिए, यहां अन्य कारणों का सारांश है कि आपको Redux का उपयोग क्यों करना चाहिए:
