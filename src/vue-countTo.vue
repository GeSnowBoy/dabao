<template>
    <div class="H100">
        <Spin size="large" fix v-if="!questionsInfo" style="min-height: 500px;"></Spin>
        <div class="paper-wrap" v-if="questionsInfo">

            <div class="paper-title">
                <span class="paper-span">{{questionsInfo.paperTitle}}</span>
                <span class="paper-sore">总分:{{questionsInfo.paperScore}}分</span>
                <span class="numberQuestions">题数:{{questionsInfo.totalNum}}</span>
                <span class="paper-timer">当前时间: {{time}} / {{totalTime}}秒</span>
                <span @click="$emit('close')" style="float:right;font-size: 30px;padding-right: 8px;">
          <i class="ivu-icon ivu-icon-ios-close"></i>
        </span>
            </div>
            <Progress :percent="time/totalTime*100" :stroke-width="3" hide-info class="progress-group"/>

            <div class="paper-question-wrap">
                <div
                        class="question"
                        :class="'question-' + question.qsType"
                        v-show="question.questionWrap.indexOf(curAnswerIndex) !== -1"
                        v-for="(question,index) in questionsInfo.questions"
                >
                    <!--判断第一种题型排列循序-->
                    <template v-if="question.qsType == 1">
                        <template>
                            <div class="question-title">{{question.qsTitle}}</div>
                            <div class="question-content por">
                                <my-audio
                                        :key="question.sourceContent"
                                        v-if="question.sourceContent"
                                        :src="getTrueAudio(question.sourceContent,paperId)"
                                ></my-audio>
                                {{question.qsContent}}
                            </div>

                            <div class="question-infos">
                                <div
                                        class="question-infos-item"
                                        v-for="(info,index1) in question.infos"
                                >
                                    <div v-if="info.infoContent"
                                         v-html="info.infoContent.replace(/\n/g,'<br />').replace(/(<br \/>)*$/g,'')"
                                         class="question-one"></div>
                                    <div class="question-answer"
                                         v-if="item.itemIndex == curAnswerIndex"
                                         v-for="(item,index2) in info.items"
                                    >
                                        <div class="por">
                                            <my-audio
                                                    v-if="item.sourceContent"
                                                    :src="getTrueAudio(item.sourceContent,paperId)"
                                                    :key="item.sourceContent"
                                            ></my-audio>
                                            <div
                                                    v-if="item.itemContent"
                                                    v-html="(item.questionIndex) + '. '+ (item.itemContent).replace(/\n/g,'<br />').replace(/(<br \/>)*$/g,'')"
                                                    class="question-div"></div>

                                        </div>

                                        <!--   答案类型 {{info.answerType}}-->
                                        <template v-if="item.answers &&  item.answers.length ">
                                            <template>
                                                <div v-for="(answer,index3) in item.answers"
                                                     style="display: block;margin-right: 30px;margin-bottom: 6px;">
                                                    <span> {{String.fromCharCode(65+index3)}} :</span>
                                                    <span
                                                            v-if="answer.answerContent "
                                                            v-html="answer.answerContent.replace(/\n/g,'<br />')"
                                                            class="answer-span">
                          </span>

                                                </div>
                                            </template>
                                        </template>
                                    </div>
                                </div>

                            </div>

                        </template>
                    </template>

                    <template v-else>
                        <template>
                            <div class="question-title">{{question.qsTitle}}</div>
                            <div class="question-content por">
                                <my-audio
                                        v-if="question.sourceContent"
                                        :key="question.sourceContent"
                                        :src="getTrueAudio(question.sourceContent,paperId)"
                                ></my-audio>
                                {{question.qsContent}}
                            </div>


                            <div class="question-infos">
                                <div
                                        class="question-infos-item"
                                        v-if="info.infoIndex == curAnswerIndex"
                                        v-for="(info,index1) in question.infos"
                                >
                                    <img v-if="info.infoContentImg" :src="getTrueAudio(info.infoContentImg,5725)"
                                         alt="">
                                    <div v-if="info.sourceContent && question.qsType == 20">
                                        <img :src="getTrueAudio(item,paperId)"
                                             v-for="item in info.sourceContent.split(',')" class="answerimg">
                                    </div>

                                    <div class="por">

                                        <div v-if="info.infoContent"
                                             v-html="info.infoContent.replace(/\n/g,'<br />').replace(/(<br \/>)*$/g,'')"
                                             class="question-one"></div>
                                        <my-audio
                                                v-if="info.sourceContent"
                                                :key="info.sourceContent"
                                                :src="getTrueAudio(info.sourceContent,paperId)"

                                        ></my-audio>

                                    </div>
                                    <div class="por">
                                        <my-audio
                                                v-if="info.infoContentSourceContent"
                                                :key="info.infoContentSourceContent"
                                                :src="getTrueAudio(info.infoContentSourceContent,paperId)"
                                        ></my-audio>
                                    </div>

                                    <div class="question-answer"
                                         v-for="(item,index2) in info.items"
                                    >
                                        <template v-if="item.itemContent">
                                            <div
                                                    v-html="(item.questionIndex) + '. '+ (item.itemContent).replace(/\n/g,'<br />').replace(/(<br \/>)*$/g,'')"
                                                    class="question-div"></div>
                                        </template>
                                        <div class="por">


                                            <my-audio
                                                    v-if="item.sourceContent"
                                                    :key="item.sourceContent"
                                                    :src="getTrueAudio(item.sourceContent,paperId)"
                                            ></my-audio>
                                        </div>

                                        <!--   答案类型 {{info.answerType}}-->
                                        <template v-if="item.answers &&  item.answers.length ">
                                            <template v-if="question.qsType != 5">
                                                <div v-for="(answer,index3) in item.answers"
                                                     style="display: block;margin-right: 30px;margin-bottom: 6px;">
                      <span
                              v-if="info.answerType == 1&&question.qsType!==19"> {{String.fromCharCode(65+index3)}} :</span>
                                                    <span v-if="answer.answerContent &&question.qsType!=19"
                                                          v-html="answer.answerContent.replace(/\n/g,'<br />')"
                                                          class="answer-span"></span>


                                                </div>
                                            </template>
                                        </template>
                                    </div>
                                </div>

                            </div>

                        </template>

                    </template>
                </div>
            </div>

            <div class="question-bottom">
                <div style="display: inline-block;" class="question-page">
                    <span @click="preQuestion" class="question-page-span " :class="{change:changes=='left'}"><</span>
                    <div class="page-number">{{curAnswerIndex + 1}} / {{questionsInfo.totalNum}}</div>
                    <span @click="nextQuestion" class="question-page-span" :class="{change:changes=='right'}">></span>
                </div>
                <div class="question-button-btn">
                    <Button type="primary" class="qustion-btn" @click="$emit('edit',paperId,questionsInfo)">进入编辑页
                    </Button>
                    <Button type="primary" class="qustion-btn" @click="$emit('commit',paperId,questionsInfo)">提交
                    </Button>
                    <Button type="primary" class="qustion-btn" @click="$emit('copy',paperId,questionsInfo)">复制</Button>
                </div>
            </div>

        </div>
        <div>
        </div>
    </div>

</template>
<script>

  import MyAudio, { control as temp } from './MyAudio.vue';

  let control = temp || window._control;
  console.log(control);

  function downTime(time, cb, success) {
    cb && cb(time);
    var temp = setInterval(() => {
      time--;
      // console.log(time);
      cb && cb(time);
      if (time <= 0) {
        clearInterval(temp);
        success && success();
      }
    }, 1000);
  }

  export default {
    components: {
      MyAudio
    },
    props: {
      paperId: {
        default: 2392
      }
    },
    mounted: function() {
      this.getPaperData();
    },
    data: function() {
      return {
        time: 0,
        questionsInfo: null,
        curAnswerIndex: 0,
        modalQuestion: true,
        changes: '',
        playStatus: 'play'

      };
    },
    methods: {

      getPaperData: function() {
        var handle = (questionsInfo) => {
          var questionIndex = 1;
          var infoIndex = -1;
          questionsInfo.questions.map((question) => {
            question.questionWrap = [];
            if (question.qsType === 1) {
              question.infos.map(info => {
                info.items.map(item => {
                  let tempIndex = ++infoIndex;
                  question.questionWrap.push(tempIndex);
                  item.itemIndex = tempIndex;
                  item.questionIndex = questionIndex++;
                });
              });

            } else {
              let isPage = [3].indexOf(question.qsType) !== -1;
              if (!isPage) {
                let tempIndex = ++infoIndex;
                question.questionWrap.push(tempIndex);
              }
              question.infos.map(info => {
                if (isPage) {
                  let tempIndex = ++infoIndex;
                  question.questionWrap.push(tempIndex);
                }
                info.infoIndex = infoIndex;
                info.items.map(item => {
                  item.questionIndex = questionIndex++;
                });
              });
            }


          });
          questionsInfo.totalNum = infoIndex + 1;
          this.questionsInfo = questionsInfo;
          console.log(this.questionsInfo);
          downTime(this.totalTime, time => {
            this.time = this.totalTime - time;
          });
        };


        // 老版本
        $.ajax({
          'url': '/paper/json/' + this.paperId,
          'method': 'GET',
          success: (res) => {
            handle(res.data);
          }
        });
        // 本地

      },
      preQuestion: function() {
        control.stop();
        if (this.curAnswerIndex > 0) {
          this.curAnswerIndex--;
        }
        this.changes = 'left';
        // this.styles = 'changs';

      },
      nextQuestion: function() {
        control.stop();
        if (this.curAnswerIndex < this.questionsInfo.totalNum - 1) {
          this.curAnswerIndex++;
        }
        this.changes = 'right';
        // this.styles = 'changs';
        // this.isActived = !this.isActived;

      },
      getTrueAudio: function(src, id) {
        let baseUrl = 'http://spokenenglishtest.smartedu.lenovo.com/media/';
        // let baseUrl = 'http://10.120.18.56:8080/media/';
        return baseUrl + id + '/Data/' + src.split('/')[src.split('/').length - 1];
      }
    },
    computed: {
      totalTime: function() {
        try {
          let time = 0;
          this.questionsInfo.questions.map(function(question) {
            question.infos.map(function(info) {
              time += parseFloat(info.infoPrepareSecond);
              info.items.map(function(item) {
                time += parseFloat(item.itemPrepareSecond);
                time += parseFloat(item.itemAnswerSecond);
              });
            });
          });
          return time;
        } catch (e) {

        }
        return 0;
      }
    },
    filters: {}
  };
</script>
<style lang="less" scoped>
    .paper-wrap {

        height: 100%;
        display: flex;
        flex-direction: column;

        .question-audio, .au {
            display: none;
        }

        .paper-title {
            background: #ccc;
            height: 44px;
            line-height: 44px;
            padding-left: 15px;
            flex: none;

            .paper-span {
                font-size: 16px;
            }

            .paper-sore {
                display: inline-block;
                font-size: 14px;
                margin-left: 25px;
            }

            .numberQuestions, .paper-timer {
                font-size: 14px;
                display: inline-block;
                margin-left: 15px;

            }

        }

        .progress-group {
            height: 4px;
            flex: none;
        }

        .paper-question-wrap {
            flex: 1;
            overflow: auto;
            padding: 0 40px;


            .question {
                &.question-19 {
                    .question-infos {

                        .question-answer {
                            display: inline-block;
                            vertical-align: top;

                            .question-div {
                                display: inline-block;
                                border: 1px solid #ccc;
                                padding: 3px 10px;
                                margin-right: 10px;
                            }
                        }

                        img {
                            display: block;
                            margin: auto;
                        }
                    }
                }

                &.question-9 {

                }

                &.question-20 .answerimg {
                    width: 155px;
                }


                .img-icon {
                    display: inline-block;
                    position: relative;
                    /* float: right; */
                    /* margin-left: 5px; */
                    left: -27px;
                    top: -30px;
                    width: 20px;
                    height: 20px;
                    padding-right: 18px !important;
                    background: url("https://spokenenglishtest.smartedu.lenovo.com/assets/common/images/icon/play.png") no-repeat center center;
                }


                .audion-one {
                    position: relative;
                    top: 59px;
                }

                .question-infos {
                    .question-answer {
                        padding-bottom: 16px;
                        text-align: left;
                    }

                    .question-infos-item {
                        .question-one {
                            padding-bottom: 10px;

                        }
                    }

                    .question-div {
                        font-size: 17px;
                        padding-bottom: 10px;
                    }
                }

                .question-title {
                    font-family: PingFangSC-Regular;
                    font-size: 18px;
                    color: #000;
                    padding: 10px 0;
                    text-align: left;
                }

                .question-content {
                    text-align: left;
                    color: #999;
                    margin-bottom: 10px;
                }
            }
        }

        .question-bottom {
            border-top: 1px solid #ccc;
            padding: 10px 18px;
            flex: none;

            .question-page {
                display: inline-block;
                height: 33px;
                line-height: 33px;

                .question-page-span {
                    display: inline-block;
                    vertical-align: middle;
                    font-size: 25px;
                    width: 41px;
                    height: 33px;
                    background: #ccc;
                    border-radius: 4px;
                    text-align: center;
                    cursor: pointer;

                    &:hover {
                        transtion: .5s all;
                        color: #2d8cf0;
                    }
                }

                .question-page-span.act {
                    background: red;
                }

                .page-number {
                    font-size: 13px;
                    display: inline-block;
                    margin: 0 4px;
                    vertical-align: middle;

                }
            }

            .question-button-btn {

                float: right;

                .qustion-btn {
                    width: 123px;
                }
            }
        }


    }


</style>
<style>
    .por {
        position: relative;
    }

    body .ivu-progress-inner {
        vertical-align: top;

    }

    .answer-img {
        display: block;
        width: 800px
    }

    .answer-img1 {
        width: 386px;
        float: left;
        height: 300px;
        display: block;
    }
</style>


