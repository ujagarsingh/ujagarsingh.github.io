---
layout: post
title:  "Building and publishing an NPM Package using Typescript"
date:   2019-03-29 10:15:00 +0530
categories: Reactjs, Javascript
img: build-NPM-package-using-typescript.jpg
categories: [Reactjs, Javascript]
---

[Source : Building and publishing an NPM Typescript Package](https://itnext.io/step-by-step-building-and-publishing-an-npm-typescript-package-44fe7164964c){:target="_blank"}

## परिचय
इस गाइड में, हम typescript में एक reusable module का निर्माण करेंगे और इसे Node.js package के रूप में published (प्रकाशित) करेंगे। यह काम कई अलग-अलग तरीकों से किया जा रहा है, इसलिए मैं आपको यह दिखाना चाहता हूं कि आप कैसे सर्वोत्तम तरीकों और tools `typescript, Tslint, Prettier और Jest` का step by step उपयोग करके अपना package बना सकते हैं।

### हम निम्न `package` को बनाएंगे :
https://www.npmjs.com/package/my-awesome-greeter
https://github.com/caki0915/my-awesome-greeter

### NPM क्या है?
Npm `Javascript और reusable software code` की दुनिया की सबसे बड़ी लाइब्रेरी  के लिए  package manager है। जैसा कि हम आगे चलकर देखेंगे की, यह भी अपने आप में  एक बढ़िया  tool है।

### Typescript ही  क्यों?
Typescript `Javascript` के लिए एक superset के रूप में, `optional typing` और `deep intellisense` प्रदान करता है। Package के development के लिए मेरी निजी राय है की, सभी package को  `Typescript` ही  में बनाना  चाहिए

आप में से कुछ महसूस कर सकते हैं कि strong typing से productivity (उत्पादकता) घट जाती है और यह उपयोग करने के प्रयास के लायक नहीं है। जब यह small-scale projects की बात आती है तो मैं सहमत हो सकता हूं, लेकिन, जब package-development की बात आती है, तो Typescript में कुछ गंभीर फायदे (advantages) हैं:

1. अधिक मजबूत कोड (robust code)लिखा जाता है  और mange रखने में आसान है।
2. package का उपयोग Typescript और Javascript दोनों उपयोगकर्ताओं के लिए किया जा सकता है! यदि आपकी लाइब्रेरी लोकप्रिय हो जाती है, तो जब कभी type-definitions की मांग होगी, और उन लोगों को manually लिखने के लिए time-consuming, error-prone और update करने में कठिन होगा।
3.package में type-definitions के साथ, user `types` को दूसरे packages से  download नहीं करना होगा।
4. strong typing अधिक self-documenting है और code अधिक  understandable होता है।
5. भले ही आपके package का उपयोग करने वाला व्यक्ति Typescript का उपयोग नहीं करता है, लेकिन **Visual Studio Code** जैसे कुछ editor अभी भी users को बेहतर जानकारी देने के लिए type-definitions का उपयोग करेंगे।

### तो चलो, शुरुआत करते हैं !
आपके पास Node और NPM का latest verison होना चाहिए ।

```javascript
node -v
10.0.0
npm -v
6.0.0
```

### कोई अच्छा नाम लेते हैं 
यह कठिन हो सकता है। package के नाम pascal-case और lowercase में होने चाहिए। चूंकि 700k + package हैं, इसलिए यह सुनिश्चित करने के लिए कि आपके  द्वारा चुना गया शानदार  नाम पहले से नहीं है, [https://www.npmjs.com/](https://www.npmjs.com/){:target="_blank"} पर search करें। इस guide के लिए, मैं  **my-awesome-greeter** का नाम लेता हूँ , लेकिन aap एक unique name का उपयोग करें, ताकि आप बाद में  अपने package को npm पर publish कर सकें।

### Basic Setup
एक उपयुक्त नाम के साथ अपने package folder बनाएँ

```javascript
mkdir my-awesome-greeter && cd my-awesome-greeter
```

### एक git repository बनाएँ
सबसे पहले। आपको अपने package के लिए remote git repository की जरूरत होगी, ताकि इसे download किया जा सके। एक remote git repository बनाना इस tutorial के दायरे से बाहर है लेकिन एक बार ऐसा करने के लिए आप local repository को initialize करने और अपने remote origin को set करने के लिए निम्न पंक्तियों का उपयोग कर सकते हैं।

```javascript
git init
echo "# My Awesome Greeter" >> README.md
git add . && git commit -m "Initial commit"
```

निम्नलिखित मैं  <Git Repository Url> को अपने remote repository url से  बदलें।

```javascript
git remote add origin <Git Repository Url>
git push -u origin master
```

### अपने package को   initialize करें
सभी default values के साथ एक **package.Json** फ़ाइल बनाएँ।
हम इसे बाद में संशोधित करने जा रहे हैं।

```javascript
npm init -y
```

अंतिम चरण के रूप में, हम एक **.gitignore** फ़ाइल को root में जोड़ने जा रहे हैं। वहां बहुत सारे **.ignignore** के टेम्पलेट हैं, लेकिन मैं इसे सरल रखना पसंद करता हूं और आपकी आवश्यकता से अधिक नहीं जोड़ता। फिलहाल, हमें केवल **node_modules** folder को ignore करना होगा।

```javascript
echo "node_modules" >> .gitignore
```

बहुत बढ़िया! हमें मूल बातें मिलीं  यह Visual Studio Code में प्रोजेक्ट को open करने  पर ऐसा दिखता है। अब से मैं console का उपयोग करने के बजाय vscode में  फ़ाइलों को जोड़ूंगा, लेकिन आपको जो पसंद हो वही चुन सकते हैं ।

### Add Typescript as a DevDependency

आइए एक निर्भरता के रूप में Typescript के साथ शुरुआत करें

```javascript
npm install --save-dev typescript
```

NPM द्वारा  typescript को **DevDependency** के रूप में install करने के लिए `--save-dev` flag का use करना होगा । DevDependency और Dependency के बीच का अंतर यह है कि, जब आप npm install चलाते हैं, तभी DevDependency install की जाएंगी, लेकिन जब end-user package को  install तब  नहीं ।

उदाहरण के लिए, package Developing करते समय Typescript की आवश्यकता होती है, लेकिन इसके द्वारा package का उपयोग करते समय इसकी आवश्यकता नहीं होती है।

अच्छा! अब आपको अपने root में एक **node_modules** folder और एक **package-lock.json** भी दिखाई देगा।

Typescript को compile करने के लिए भी हमें **tsconfig.json** file की आवश्यकता होती है, इसलिए इसे प्रोजेक्ट root में जोड़ें:

```javascript
{
  "compilerOptions": {
    "target": "es5",
    "module": "commonjs",
    "declaration": true,
    "outDir": "./lib",
    "strict": true
  },
  "include": ["src"],
  "exclude": ["node_modules", "**/__tests__/*"]
}
```

#### यहां बहुत सारी चीजें चल रही हैं, इसलिए आइये हमारी **config** file को समझते हैं :
**target**: हम es5 के बाद से compile करना चाहते हैं जब तक हम browser compatibility के साथ एक package बनाना चाहते हैं।
**module**: compatibility के लिए commonjs का उपयोग करें।
**declaration**: जब आप package बनाते हैं, तो यह true होना चाहिए। ऐसा करने से Typescript तब compile करते समय  `Javascript code` और  type definitions एक साथ  export करेगा ताकि package का उपयोग Typescript और Javascript दोनों के साथ किया जा सके।
**outDir**: Javascript को lib folder में compile किया जाएगा।
**include**: सभी source फ़ाइलें `src` folder में  होंगी ।
**exclude**: हम node_modules को  transpile नहीं करना चाहते हैं, न ही tests करना चाहते हैं, क्योंकि ये केवल development के दौरान उपयोग किए जाते हैं।

### Your first code!
अब जब हमारे पास compilation setup है, तो हम अपनी पहली code की  line को जोड़ सकते हैं।
तो आइए  root में एक `src` फ़ोल्डर बनाएं और एक index.ts फ़ाइल जोड़ें:

```javascript
export const Greeter = (name: string) => `Hello ${name}`; 
```

ठीक है, यह एक अच्छी शुरुआत है। अगला कदम एक build script को **package.json** में जोड़ना है:

```javascript
"build" : "tsc"
```

अब आप console में build command चला सकते हैं:

```javascript
npm run build
```

आपको अपने  root में एक नया `lib` folder के साथ compiled code और type definitions दिखाई देगा!

### Ignore compiled code in git
**package-lock.json** को छोड़कर, आप आमतौर पर source control में  auto-generated files नहीं चाहते हैं। यह हर बार autogenerated होने पर अनावश्यक conflicts पैदा कर सकता है। आइए **lib** folder में `.gitignore` जोड़ें:

```javascript
node_modules
/ lib
```

`lib` से पहले slash (/) का अर्थ है "root के top lavel में केवल lib folder को अनदेखा करें" यही हम इस मामले में चाहते हैं।

### Formatting and Linting
एक अच्छे package में linting और formatting के लिए सख्त नियम शामिल होने चाहिए। खासकर यदि आप बाद में और अधिक सहयोगी (collaborators)चाहते हैं। `Prettier` और `TsLint` को जोड़ने दें!

**linting**: Linting एक programe चलाने की प्रक्रिया है जो संभावित  errors के लिए code का विश्लेषण (potential) करती है ।


Typescript की तरह, ये package केवल  development के लिए उपयोग किए जाने वाले tools हैं। उन्हें भी devDependencies के रूप में जोड़ा जाना चाहिए:

```javascript
npm install --save-dev prettier tslint tslint-config-prettier
```

**tslint-config-prettier** एक preset है जिसकी हमें आवश्यकता है क्योंकि यह `tslint` और `prettiers` के formatting rules के  conflicts से रोकता है।

अतः  root में, **tslint.json** जोड़ेंगे :

```javascript
{
   "extends": ["tslint:recommended", "tslint-config-prettier"]
}
```

और .prettierrc

```javascript
{
  "printWidth": 120,
  "trailingComma": "all",
  "singleQuote": true
}
```

अंत में, package.json में **lint** और **format ** script जोड़ेंगे 

```javascript
"format": "prettier --write \"src/**/*.ts\" \"src/**/*.js\"",
"lint": "tslint -p tsconfig.json"
```

आपका package.json अब कुछ इस तरह दिखना चाहिए:

अब आप console में `npm run lint` और `npm run format` चला सकते हैं:

```javascript
npm run lint
npm run format
```

### अपने package में ज़रूरत से ज़्यादा शामिल न करें!
हमारी **.gitignore** file में, हमने  `/ git` repository में build-files न चाहते हुए भी जोड़ दीं। विपरीत एक published package के लिए जाता है। हमें source code नहीं चाहिए, केवल **build-files**!

इसे दो तरीकों से solve किया जा सकता है। एक तरीका एक **.npmignore** file में `files/folders` को blacklist करने का है। हमारे मामले में कुछ इस तरह देखा जाना चाहिए:

```javascript
src
tsconfig.json
tslint.json
.prettierrc
```

हालाँकि, files को blacklist करना एक अच्छा तरीका नहीं है। root में जोड़ी गई हर नई file/folder को **.npmignore** file में भी जोड़ना होगा! इसके बजाय, आपको उन files/folders को `whitelist` में डालना चाहिए जिन्हें आप publish करना चाहते हैं। यह  package.json में  **flies** property को जोड़कर किया जा सकता है ।:

```javascript
“files”: [“lib/**/*”]
```

ऐसा करने के लिए , केवल `lib` folder को  published package में  जोड़ेंगे ! (README.md और package.json डिफ़ॉल्ट रूप से जोड़े जाते हैं)।

NPM packages में **whitelist** के बारे में अधिक जानकारी के लिए, इस पोस्ट को NPM ब्लॉग से देखें। [publishing what you mean to publish](https://blog.npmjs.org/post/165769683050/publishing-what-you-mean-to-publish){:target="_blank"}

### Setup Testing with Jest

एक अच्छे  package में unit tests शामिल होना चाहिए! हमें Jest को जोड़ना चाहिए  (यह  facebook द्वारा बनाया हुआ एक अच्छा testing framework है )

चूंकि हम अपने Typescript source-files के खिलाफ tests लिख रहे हैं, इसलिए हमें `ts-jest` और `@type/jest` भी जोड़ना होगा। परीक्षण सूट का उपयोग केवल विकास के दौरान किया जाता है, इसलिए उन्हें अवमूल्यन के रूप में जोड़ें

```javascript
npm install --save-dev jest ts-jest @types/jest
```

ठंडा! अब हमें जेस्ट को कॉन्फ़िगर करने की आवश्यकता है। आप एक jest सेक्शन को package.json में लिखना या एक अलग कॉन्फिग फाइल बनाना चुन सकते हैं। हम इसे एक अलग फ़ाइल में जोड़ने जा रहे हैं, इसलिए जब हम package प्रकाशित करेंगे तो यह शामिल नहीं होगा।

रूट में एक नई फ़ाइल बनाएँ और इसे jestconfig.json नाम दें:

```javascript
{
  "transform": {
    "^.+\\.(t|j)sx?$": "ts-jest"
  },
  "testRegex": "(/__tests__/.*|(\\.|/)(test|spec))\\.(jsx?|tsx?)$",
  "moduleFileExtensions": ["ts", "tsx", "js", "jsx", "json", "node"]
}
```

Package.json में पुरानी परीक्षण स्क्रिप्ट निकालें और इसे इसमें बदलें:

```javascript
"test": "jest --config jestconfig.json",
```
Package.json कुछ इस तरह दिखना चाहिए:


एक मूल परीक्षण लिखें
यह हमारी पहली परीक्षा लिखने का समय है! 😃

Src फ़ोल्डर में, __tests__ नामक एक नया फ़ोल्डर जोड़ें और उसके अंदर, आपको पसंद किए गए नाम के साथ एक नई फ़ाइल जोड़ें, लेकिन इसे test.ts के साथ समाप्त होना है, उदाहरण के लिए Greeter.test.ts

```javascript
import { Greeter } from '../index';
test('My Greeter', () => {
  expect(Greeter('Carl')).toBe('Hello Carl');
});
```
ठीक है, तो केवल एक चीज जो हम यहां कर रहे हैं वह यह सत्यापित करना है कि यदि इनपुट कार्ल है, तो हमारा तरीका नमस्ते कार्ल वापस आ जाएगा।

अब, चलाने का प्रयास करें

npm परीक्षण

कूल यह काम करता है! जैसा कि आप देख सकते हैं कि हमने एक परीक्षा पास की है।

NPM में मैजिक स्क्रिप्ट का उपयोग करें
एक भयानक package के लिए, हमें निश्चित रूप से जितना संभव हो उतना स्वचालित करना चाहिए। हम npm में कुछ स्क्रिप्ट्स को खोदने के बारे में हैं: तैयारी, पूर्वनिर्धारित, प्रचलित, संस्करण और पोस्टवॉर्शन

तैयार दोनों पहले से ही चलेगा package पैक और प्रकाशित किया गया है, और स्थानीय npm पर स्थापित करें। कोड के निर्माण के लिए बिल्कुल सही। इस स्क्रिप्ट को package.json में जोड़ें

"तैयार": "एनपीएम रन बिल्ड"
पहले से तैयार हो जाएगा और पहले से ही npm प्रकाशित पर तैयार होगा। यहां हम यह सुनिश्चित करने के लिए कि हम खराब कोड प्रकाशित नहीं करेंगे, यह सुनिश्चित करने के लिए हम अपना परीक्षण और एक बार चलेंगे:

"प्रीप्रोबायॉनली": "एनपीएम टेस्ट और& एनपीएम रन लिंट"
नए package संस्करण को टक्कर देने से पहले प्रबलता चलेगी। यह सुनिश्चित करने के लिए कि हम खराब कोड वाले संस्करण से नहीं टकरा रहे हैं, यहाँ भी क्यों नहीं चला? 😃

"प्रचलित": "एनपीएम रन लिंट"
नया संस्करण टकरा जाने के बाद संस्करण चलेगा। यदि आपके package में एक जीआईटी भंडार है, जैसे हमारे मामले में। हर बार जब आप एक नया संस्करण बनाते हैं, तो एक प्रतिबद्ध और एक नया संस्करण-टैग बनाया जाएगा। यह कमांड पहले से ही चलेगी जब कमिट किया जाता है। एक विचार यह है कि यहाँ फॉर्मेटर चलाना है और इसलिए कोई भी बदसूरत कोड नए संस्करण में नहीं जाएगा:

"संस्करण": "npm रन फॉर्मेट और& git add -A src"
कमिटमेंट के बाद पोस्टवॉर्शन चलेगा। टैग के साथ-साथ कमिट को आगे बढ़ाने के लिए एक सही जगह।

```javascript
"postversion" : "git push && git push --tags"
```

package में मेरी स्क्रिप्ट अनुभाग इस प्रकार है:

```javascript
"scripts": {
   "test": "jest --config jestconfig.json",
   "build": "tsc",
   "format": "prettier --write \"src/**/*.ts\" \"src/**/*.js\"",
   "lint": "tslint -p tsconfig.json",
   "prepare": "npm run build",
   "prepublishOnly": "npm test && npm run lint",
   "preversion": "npm run lint",
   "version": "npm run format && git add -A src",
   "postversion": "git push && git push --tags"
}
```
package खत्म करना। json
हमारे भयानक package को खत्म करने का आखिरकार समय आ गया है! सबसे पहले, हमें अपने package में कुछ बदलाव करने की जरूरत है। फिर से देखें:

```javascript
{
   "name": "my-awesome-greeter",
   "version": "1.0.0",
   "description": "A nice greeter",
   "main": "lib/index.js",
   "types": "lib/index.d.ts",
   "scripts": {
      "test": "jest --config jestconfig.json",
      "build": "tsc",
      "format": "prettier --write \"src/**/*.ts\" \"src/**/*.js\"",
      "lint": "tslint -p tsconfig.json",
      "prepare": "npm run build",
      "prepublishOnly": "npm test && npm run lint",
      "preversion": "npm run lint",
      "version": "npm run format && git add -A src",
      "postversion": "git push && git push --tags"
   },
   "repository": {
     "type": "git",
     "url": "git+https://github.com/caki0915/my-awesome-greeter.git"
   },
   "keywords": ["Hello", "Greeter"],
   "author": "C-J",
   ....
```

यहाँ हम एक अच्छा विवरण, एक लेखक और कुछ प्रासंगिक कीवर्ड जोड़ रहे हैं। यहाँ मुख्य मुख्य महत्वपूर्ण है क्योंकि यह npm को बताएगा कि यह कहाँ से मॉड्यूल आयात कर सकता है।
प्रमुख प्रकार Typescript और कोड-संपादकों को बताएंगे जहां हम प्रकार की परिभाषाएं पा सकते हैं!

प्रतिबद्ध और अपने कोड को धक्का देने के लिए
अपने दूरस्थ रिपॉजिटरी के लिए अपने सभी काम को आगे बढ़ाने का समय! यदि आपने अपना नवीनतम कोड पहले से नहीं बनाया है, तो अब यह करने का समय है। 😉

git add -A && git कमिट-मी "सेटअप package"
जोर का धक्का
आप एनपीएम के लिए package प्रकाशित करें!
अपना package प्रकाशित करने के लिए, आपको एक एनपीएम खाता बनाना होगा।
यदि आपके पास कोई खाता नहीं है तो आप https://www.npmjs.com/signup पर ऐसा कर सकते हैं
या कमांड चलाएँ: npm एड्यूसर

यदि आपके पास पहले से ही खाता है, तो एनपीएम खाते में प्रवेश करने के लिए एनपीएम लॉगिन चलाएं।


मेरे मौजूदा एनपीएम खाते में प्रवेश करना
ठीक है! अब प्रकाशित करें।

npm प्रकाशित
जैसा कि आप देख सकते हैं कि package पहले तैयार स्क्रिप्ट द्वारा बनाया जाएगा, फिर package प्रकाशित होने से पहले परीक्षण और लिंट प्रीप्रोजेक्टली स्क्रिप्ट द्वारा चलेगा।


अपना package देखें
अब npmjs पर अपने package को ब्राउज़ करें। Url मेरे मामले में https://npmjs.com/package/ <your-package-name> है
https://npmjs.com/package/my-awesome-greeter


अच्छा! हमें एक package मिला got good अब तक अच्छा लग रहा है!

एक नया संस्करण टकरा रहा है
package का एक नया पैच संस्करण दें:

```javascript
npm version patch
```

हमारा प्रिवेंशन, वर्जन और पोस्टवर्सन चलेगा, git में एक नया टैग बनाएगा और इसे हमारे रिमोट रिपॉजिटरी में धकेल देगा। अब फिर से प्रकाशित करें:

```javascript
npm publish
```
और अब आपके पास एक नया संस्करण है

### What's next?
इस ट्यूटोरियल के स्कोप के लिए, मैं यहाँ पर कुछ के लिए रुकना चाहूँगा जिसे मैं "एनपीएम package के लिए न्यूनतम सेटअप" कहूंगा। हालाँकि, जब आपका package बढ़ता है तो मैं सिफारिश करूंगा:

1. ट्रैविस के साथ स्वचालित बिल्ड सेटअप करें
2. कोडेकोव के साथ कोड-कवरेज का विश्लेषण करें
3. अपनी रीडमी में शील्ड्स के साथ बैज जोड़ें। सभी को बैज पसंद है 😎

लेकिन दूसरे ट्यूटोरियल के लिए इसे छोड़ दें।