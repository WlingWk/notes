# 需要安装ivew，使用了其中的弹窗
 - $ npm install view-design --save
 
 在main.js 中 ：
 - import ViewUI from 'view-design';
 - import 'view-design/dist/styles/iview.css';
 - import './assets/css/common.css'

在vue页面中注册组件
 - import Calculator from '@/components/public/Calculator';
 - 在components注册：components:{Calculator}
 - 在需要的位置直接使用
 <Calculator @hide-calc="hideCalc" :show="showCalc" />

 - hideCalc是用于与该组件通信，来显示和隐藏该组件，写在methods中
 - showCalc为data中的变量