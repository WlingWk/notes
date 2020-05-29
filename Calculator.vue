<template>
    <div class="calc">
        <Modal
        draggable
        width="300"
        v-model="show"
        title="计算器"
        :closable="false"
        footer-hide>
        <p slot="header">
            <span>计算器</span>
            <Icon @click="hide" class="modal-close" type="md-close" />
        </p>
        <Row style="margin-bottom: 10px;">
          <Col span="24"><div class="input screen"> {{num1}}{{opt}}{{num2}}</div></Col>
          <Col span="24"><div class="res screen">{{res}}</div></Col>
        </Row>
        <Row :gutter="10">
            <Col v-for="(item,index) in matrix" span="6">
                <div @click="input(item)" class="col" >
                    {{item.wd === 'del'?"":item.wd=='^'?'x':item.wd=='sqrt'?'√':item.wd}}
                    <Icon v-if="item.wd=='del'" type="md-backspace" />
                    <span style="font-size: 10px;position: relative;top: -5px;left: -3px;" v-if="item.wd=='^'" class="square">2</span>
                </div>
            </Col>
        </Row>
    </Modal>
    </div>
</template>
<script>
    export default {
        name: 'Calculator',
        props: {
            show: Boolean
        },
        data() {
            return {
                num1: '',
                num2: '',
                opt: '',
                res: '',
                matrix: [{
                    wd: 7,
                    type: 'num'
                }, {
                    wd: 8,
                    type: 'num'
                }, {
                    wd: 9,
                    type: 'num'
                }, {
                    wd: '+',
                    type: 'add'
                }, {
                    wd: 4,
                    type: 'num'
                }, {
                    wd: 5,
                    type: 'num'
                }, {
                    wd: 6,
                    type: 'num'
                }, {
                    wd: '-',
                    type: 'sub'
                }, {
                    wd: 1,
                    type: 'num'
                }, {
                    wd: 2,
                    type: 'num'
                }, {
                    wd: 3,
                    type: 'num'
                }, {
                    wd: '*',
                    type: 'multi'
                }, {
                    wd: '.',
                    type: 'dot'
                }, {
                    wd: 0,
                    type: 'num'
                }, {
                    wd: 'c',
                    type: 'clear'
                }, {
                    wd: '/',
                    type: 'division'
                }, {
                    wd: '^',
                    type: 'square'
                }, {
                    wd: 'sqrt',
                    type: 'sqrt'
                }, {
                    wd: '=',
                    type: 'equal'
                }, {
                    wd: 'del',
                    type: 'delete'
                }]
            }
        },
        //x^2 ,x^3,根号，x^y,ln,sin,cos,tan
        methods: {
            hide: function() {
                this.$emit('hide-calc')
            },
            doCalc(n1, opt, n2) {
                let res = '';
                n1 = String(n1);
                n2 = String(n2)
                const len1 = n1.indexOf('.') > -1 ? n1.split('.')[1].length : 0;
                const len2 = n2.indexOf('.') > -1 ? n2.split('.')[1].length : 0;
                const len = Math.max(len1, len2);
                const tenTime = Math.pow(10, len);
                n1 = n1 * tenTime;
                n2 = n2 * tenTime;
                switch (opt) {
                    case '+':
                        res = (n1 + n2) / tenTime
                        break;
                    case '-':
                        res = (n1 - n2) / tenTime
                        break;
                    case "*":
                        res = (n1 * n2) / Math.pow(tenTime, 2)
                        break;
                    case "square":
                        res = Math.pow(n1, 2) / Math.pow(tenTime, 2);
                        break;
                    case "sqrt":
                        res = Math.sqrt(n1 / tenTime)
                        break;
                    default:
                        res = (n1 / n2)
                        break;
                }
                return res
            },
            input: function(e) {
                switch (e.type) {
                    case "num":
                    case "percent":
                    case "dot":

                        if (this.opt === '') {
                            if (e.type == 'dot') {
                                if (this.num1.indexOf('.') > -1) {
                                    return
                                }
                            }
                            this.num1 += String(e.wd);
                        } else {
                            if (e.type == 'dot') {
                                if (this.num2.indexOf('.') > -1) {
                                    return
                                }
                            }
                            this.num2 += String(e.wd);
                        }
                        if (this.opt == "") {
                            this.res = this.num1
                        } else {
                            this.res = this.doCalc(this.num1, this.opt, this.num2)
                        }
                        break;
                    case "delete":
                        if (this.num2 != '') {
                            this.num2 = this.num2.substring(0, this.num2.length - 1);
                            this.res = this.doCalc(this.num1, this.opt, this.num2)
                        } else {
                            if (this.opt != '') {
                                this.opt = ''
                            } else {
                                if (this.num1 != '') {
                                    this.num1 = String(this.num1)
                                    this.res = this.num1 = this.num1.substring(0, this.num1.length - 1);
                                }
                            }
                        }
                        break;
                    case 'clear':
                        this.num1 = '';
                        this.opt = '';
                        this.num2 = '';
                        this.res = '';
                        break;
                    case 'equal':
                    case "add":
                    case "sub":
                    case "multi":
                    case "division":
                        if (this.num1 === '.') return
                        if (this.opt != '') {
                            this.num2 = Number(this.num2);
                            this.num1 = Number(this.num1);
                            this.num1 = this.doCalc(this.num1, this.opt, this.num2);
                            this.num2 = '';
                            this.res = '';
                            this.opt = '';
                        }
                        if (e.type == 'equal') {
                            this.opt = ''
                        } else {
                            this.opt = e.wd
                        }
                        break;
                    case 'square':
                    case 'sqrt':
                        if (e.type === 'sqrt' && this.num1 < 0) {
                            this.num1 = '错误';
                            return
                        }
                        if (this.num1 === '.') return
                        this.num1 = this.doCalc(this.num1, e.type, this.num2);
                        this.res = this.num2 = this.opt = '';
                        break
                    default:
                        break;
                }
            }
        },
        mounted() {
            console.log(this.showCalc)
        },
        watch: {
            show: function() {
                console.log(this.show)
            }
        }
    }
</script>
<style scoped lang="less">
    .modal-close {
        float: right;
        cursor: pointer;
    }
    
    .screen {
        text-align: right;
        padding-bottom: 10px;
        background-color: #eee;
        padding: 5px;
        height: 30px;
        overflow: hidden;
    }
    
    .input {
        font-size: 18px;
    }
    
    .col {
        text-align: center;
        cursor: pointer;
        margin: 5px 0;
        box-shadow: 0 0 4px rgba(0, 0, 0, .15);
        /* background-color: chocolate; */
    }
</style>