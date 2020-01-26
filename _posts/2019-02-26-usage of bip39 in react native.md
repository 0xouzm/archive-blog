---
title: "usage of bip39 in react native"
date: 2019-02-26 15:08
categories: react-native
tags: block chain
---




## usage of bip39 in react native

notes:

- use npm install instead of yarn add 
- anytime you get errors like **throw err**,install again will help.（yarn and rn-nodeify --hack --install several times ,in my case..）




steps:

1. `npm i --save-dev rn-nodeify`

2. add `"postinstall": "rn-nodeify --install fs,dgram,process,path,console --hack"`in package.json

3. ```yarn add react-native-bip39 react-native-crypto react-native-randombytes ```

4. ```
   react-native link
   ```

5. ```
   ./node_modules/.bin/rn-nodeify --hack --install
   ```

6. ```js
   // index.ios.js or index.android.js
   // make sure you use `import` and not `require`!
   import './shim.js'
   // ...the rest of your code
   // import crypto from 'crypto'
   // use crypto 
   ```

7. yarn

8. if you found stream/http/proxy etc. module not found error ,try exec `./node_modules/.bin/rn-nodeify --hack --install`again, then run-android ,after getting into the welcome page successfully, copy App.js demo to yours

9. App.js:

   ```js
   import React, {Component} from 'react';
   import {Platform, StyleSheet, Text, View} from 'react-native';
   import './shim.js'
   import bip39 from 'react-native-bip39'
   
   type Props = {};
   export default class App extends Component<Props> {
       constructor(props) {
           super(props)
           this.state = {
               phrase: ''
           }
       }
   
       componentDidMount(){
           this.generateMnemonic()
       }
   
       generateMnemonic() {
           bip39.generateMnemonic()
               .then(result => {
                   this.setState({
                       phrase: result
                   })
               })
               .catch(error => {
                   console.log(error)
               })
       }
   
       render() {
           console.log(this.state.phrase)
           return (
               <View>
                   <Text>{this.state.phrase}</Text>
               </View>
           );
       }
   }
   ```
