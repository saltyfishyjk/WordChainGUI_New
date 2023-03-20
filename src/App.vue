<template>
  <v-app style="min-height: '800px'">
    <v-main id="main">
      <v-dialog v-model="hasError">
        <v-card>

          <p>
            {{ errorMessage }}
          </p>
          <v-col>
            <v-btn rounded color="blue" dark @click="hasError = false">确定</v-btn>
          </v-col>
        </v-card>
      </v-dialog>
      <v-container fluid class="fill-height">
        <div>
          <v-row>
            <v-col>
              <v-row>
                <v-btn class="mx-2" fab dark large color="green" @click="nDialog()">
                  -n
                </v-btn>

                <v-btn class="mx-2" fab dark large color="blue-grey" @click.stop="wDialog = true">
                  -w
                </v-btn>
                <v-dialog v-model="wDialog">
                  <v-card>
                    <v-text-field style="width:50%" label="首字母限制 -h" class="my-0 " v-model="head" />
                    <v-text-field style="width:50%" label="尾字母限制 -h" class="my-0 " v-model="tail" />
                    <v-text-field style="width:50%" label="不允许出现的首字母限制 -h" class="my-0 " v-model="reject" />
                    <v-checkbox label="允许单词环 -r" class="mt-0" v-model="loop"></v-checkbox>
                    <v-col>
                      <v-btn rounded color="blue" dark @click="wDialogConfirm(1)">确定</v-btn>
                      <v-btn rounded color="green" dark @click="wDialogCancel()">取消</v-btn>
                    </v-col>
                  </v-card>
                </v-dialog>
                <v-btn class=" mx-2" fab dark large color="teal" @click.stop="cDialog = true">
                  -c
                </v-btn>
                <v-dialog v-model="cDialog">
                  <v-card>
                    <v-text-field style="width:50%" label="首字母限制 -h" class="my-0 " v-model="head" />
                    <v-text-field style="width:50%" label="尾字母限制 -h" class="my-0 " v-model="tail" />
                    <v-text-field style="width:50%" label="不允许出现的首字母限制 -h" class="my-0 " v-model="reject" />
                    <v-checkbox label="允许单词环 -r" class="mt-0" v-model="loop"></v-checkbox>
                    <v-col>
                      <v-btn rounded color="blue" dark @click="cDialogConfirm(2)">确定</v-btn>
                      <v-btn rounded color="green" dark @click="cDialogCancel()">取消</v-btn>
                    </v-col>
                  </v-card>
                </v-dialog>
              </v-row>
              <v-row>
                <v-card>
                  <p class="mb-1 headline">
                  当前参数
                </p>
                {{ argsArray[mode] }} -h:{{ head }} -t:{{ tail }} -j: {{ reject }} -r: {{ loop }}
                </v-card>
              </v-row>
            </v-col>
            <v-col>
              <div>
                <v-btn rounded color="green" dark @click="textBox = true">文本框输入</v-btn>
                <v-dialog v-model="textBox">
                  <v-card>
                    <p class="mb-1 headline">
                      文本框输入
                    </p>
                    <v-textarea filled no-resize placeholder="输入单词" v-model="inputText">
                    </v-textarea>
                    <v-col>
                      <v-btn rounded color="blue" dark @click="inputTextConfirm()">确定</v-btn>
                      <v-btn rounded color="green" dark @click="inputTextCancel()">取消</v-btn>
                    </v-col>
                  </v-card>
                </v-dialog>
                <v-btn rounded color="blue-grey" dark @click="importText()">文件导入</v-btn>
                
                
    

                <v-btn rounded color="secondary" dark @click="exportText()">文件导出</v-btn>

                <v-btn rounded color="blue" dark @click="calc()">计算</v-btn>
              </div>
            </v-col>
          </v-row>
          <v-row style="height: 80%">
            <v-col style="width: 33%">
              <p class="mb-1 headline">
                <v-icon color="primary" large>mdi-polymer</v-icon>
                参数说明
              </p>
              <v-card>
                <v-tabs v-model="tab" background-color="primary" dark show-arrows>
                  <v-tab v-for="item in items" :key="item.tab">
                    {{ item.tab }}
                  </v-tab>
                </v-tabs>
                <v-tabs-items v-model="tab">
                  <v-tab-item>
                    <v-card flat>
                      <v-card-title class="headline">计算单词文本中可以构成多少个单词链（能够构成所有单词链的数目）</v-card-title>
                      <v-card-text>
                        <p>
                          -n参数统计该单词文本中共有多少条单词链，包含嵌套单词链
                        </p>
                        <p>
                          参数-n不要求和其他参数联合使用
                        </p>
                      </v-card-text>
                    </v-card>
                  </v-tab-item>
                  <v-tab-item>
                    <v-card flat>
                      <v-card-title class="headline">计算最多单词数量的单词链</v-card-title>
                      <v-card-text>
                        <p>
                          -w参数加文件名的形式计算最多单词数量的英语单词链
                        </p>
                        <p>
                          需要保证单词的输出顺序满足其单词链顺序，即首尾相连
                        </p>
                        <p>
                          假如可能有多组最长的相连英语单词串，选取其中任意一组作为结果即可
                        </p>
                        <p>
                          参数-w与-n -c 等功能性参数不兼容
                        </p>
                      </v-card-text>
                    </v-card>
                  </v-tab-item>
                  <v-tab-item>
                    <v-card flat>
                      <v-card-title class="headline">计算字母最多的单词链</v-card-title>
                      <v-card-text>
                        <p>
                          -c参数计算字母最多的英语单词链
                        </p>
                        <p>
                          需要保证单词的输出顺序满足其单词链顺序，即首尾相连
                        </p>
                        <p>
                          假如可能有多组字母数最多的单词链，选取其中任意一组作为结果即可
                        </p>
                        <p>
                          参数-c与-n -w这一类功能型参数不兼容，同时出现属于异常
                        </p>
                      </v-card-text>
                    </v-card>
                  </v-tab-item>
                  <v-tab-item>
                    <v-card flat>
                      <v-card-title class="headline">指定单词链开头字母</v-card-title>
                      <v-card-text>
                        <p>
                          -c参数指定单词链的首字母
                        </p>
                        <p>
                          参数-h属于附加型参数，单独出现属于异常
                        </p>
                        <p>
                          假参数-h与-t兼容，允许复合使用，此时需要同时满足首字母和尾字母条件
                        </p>
                      </v-card-text>
                    </v-card>
                  </v-tab-item>
                  <v-tab-item>
                    <v-card flat>
                      <v-card-title class="headline">指定单词链开头字母</v-card-title>
                      <v-card-text>
                        <p>
                          -h参数指定单词链的首字母
                        </p>
                        <p>
                          参数-h属于附加型参数，单独出现属于异常
                        </p>
                        <p>
                          参数-h与-t兼容，允许复合使用，此时需要同时满足首字母和尾字母条件
                        </p>
                      </v-card-text>
                    </v-card>
                  </v-tab-item>
                  <v-tab-item>
                    <v-card flat>
                      <v-card-title class="headline">指定单词链结尾字母</v-card-title>
                      <v-card-text>
                        <p>
                          -t参数指定单词链的尾字母
                        </p>
                        <p>
                          参数-t属于附加型参数，单独出现属于异常
                        </p>
                        <p>
                          参数-h与-t兼容，允许复合使用，此时需要同时满足首字母和尾字母条件
                        </p>
                      </v-card-text>
                    </v-card>
                  </v-tab-item>
                  <v-tab-item>
                    <v-card flat>
                      <v-card-title class="headline">指定单词链中所有单词均不允许出现的首字母</v-card-title>
                      <v-card-text>
                        <p>
                          -j参数指定不允许出现的首字母
                        </p>
                        <p>
                          参数-j属于附加型参数，单独出现属于异常
                        </p>
                        <p>
                          参数-j与参数-h -t兼容，若-h与-j指定字母一致则以“不存在符合条件的单词链”处理
                        </p>
                      </v-card-text>
                    </v-card>
                  </v-tab-item>
                  <v-tab-item>
                    <v-card flat>
                      <v-card-title class="headline">允许单词文本隐含单词环</v-card-title>
                      <v-card-text>
                        <p>
                          -r参数表示允许单词文本隐含单词环
                        </p>
                        <p>
                          参数-r属于附加型参数，单独出现属于异常
                        </p>
                        <p>
                          参数-r与参数-h及-t兼容，允许复合使用，此时需要同时满足首字母和尾字母条件
                        </p>
                        <p>
                          特别指出，-r参数可能与-j参数复合使用，同时可能存在-h以及-t参数。这四个参数作为附加型参数，若出现逻辑上的冲突，以“不存在符合条件的单词链”处理。
                        </p>
                      </v-card-text>
                    </v-card>
                  </v-tab-item>
                </v-tabs-items>
              </v-card>
            </v-col>

            <v-col style="width: 33%">
              <p class="mb-1 headline">
                输入
              </p>
              <v-card-text>
                <v-textarea filled height="100%" placeholder="输入" style="height: 100%" readonly v-model="inputText" />
              </v-card-text>
            </v-col>
            <v-col style="width: 33%">
              <p class="mb-1 headline">
                输出
              </p>
              <v-card-text>
                <v-textarea filled height="100%" placeholder="结果" style="height: 100%" readonly v-model="outputText" />
              </v-card-text>
              <v-chip class="ml-4 mb-5 mr-4 elevation-0 py-0" color="green" style="flex-grow: 0" outlined>
                <v-icon left>mdi-clock-check-outline</v-icon>
                计算用时：{{ time }} ms
              </v-chip>
            </v-col>
          </v-row>
        </div>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>

const path = window.require('path')
const ffi = window.require('ffi-napi')
const corePtr = ffi.DynamicLibrary(path.resolve('./core.dll')).get('vuetifyAPI')
const core = ffi.ForeignFunction(corePtr, 'string', ['string', 'int', 'char', 'char', 'char', 'bool'])
const moment = window.require('moment')


export default {
  name: 'App',
  data: () => ({
    tab: null,
    xlsxFile: '',
    items: [
      { tab: '-n' },
      { tab: '-w' },
      { tab: '-c' },
      { tab: '-h' },
      { tab: '-t' },
      { tab: '-j' },
      { tab: '-r' },
    ],
    mode: 0, // 0-n 1-w 2-c
    loop: false,
    head: '',
    tail: '',
    reject: '',
    argsArray: ['-n', '-w', '-c'],
    wDialog: false,
    cDialog: false,
    textBox: false,
    inputText: '',
    outputText: '',
    time: '',
    hasError: false,
    errorMessage: '',
  }),

  methods: {
   
    nDialog() {
      this.mode = 0
      this.head = ''
      this.tail = ''
      this.reject = ''
      this.loop = false
    },
    wDialogConfirm(a) {
      this.mode = a
      this.wDialog = false
    },
    wDialogCancel() {
      this.head = ''
      this.tail = ''
      this.reject = ''
      this.loop = false
      this.wDialog = false
    },
    cDialogConfirm(a) {
      this.mode = a
      this.cDialog = false
    },
    cDialogCancel() {
      this.head = ''
      this.tail = ''
      this.reject = ''
      this.loop = false
      this.cDialog = false
    },
    inputTextConfirm() {
      this.textBox = false
    },
    inputTextCancel() {
      this.textBox = false
      this.inputText = ''
    },
    reportError(message) {
      this.hasError = true
      this.errorMessage = message
    },
    importText() {
      const { dialog } = require('@electron/remote')
      const fs = require('fs')
      const path = require('path')
      dialog.showOpenDialog({
        title: '导入文件',
        buttonLabel: '导入',
        filters: [{ name: '文本文件', extensions: ['txt'] }],
      }).then(e => {
        if (e.canceled === false) {
          fs.readFile(
            path.resolve(e.filePaths[0]),
            'utf-8',
            (err, data) => {
              if (err) this.reportError(err.message);
              this.inputText = data.toString();
            }
          )
        }
      })
    },
    exportText() {
      const { dialog } = require('@electron/remote')
      const fs = require('fs')
      const path = require('path')
      dialog.showOpenDialog({
        title: '文件导出',
        buttonLabel: '保存',
        filters: [{ name: '文本文件', extensions: ['txt'] }],
        
      }).then(e => {
        if (e.canceled === false) {
          fs.writeFile(
            path.resolve(e.filePaths[0]),
            this.outputText,
            'utf-8',
          )
        }
      })
    },
    calc() {
      this.outputText = ''
      let start = moment()
      if (this.head.length > 1) {
        this.reportError("参数异常：-h参数须为长度为1的英文字母，当前输入长度大于1")
        this.calculating = false
      } else if (this.tail.length > 1) {
        this.reportError("参数异常：-t参数须为长度为1的英文字母，当前输入长度大于1")
        this.calculating = false
      } else if (this.reject.length > 1) {
        this.reportError("参数异常：-j参数须为长度为1的英文字母，当前输入长度大于1")
        this.calculating = false
      } else if (!(this.head.length == 0 || (this.head.charCodeAt(0) >= "a".charCodeAt(0) && this.head.charCodeAt(0) <= "z".charCodeAt(0))
        || (this.head.charCodeAt(0) >= "A".charCodeAt(0) && this.head.charCodeAt(0) <= "Z".charCodeAt(0)))) {
        this.reportError("参数异常：-h参数须为长度为1的英文字母，当前输入非英文字母")
        this.calculating = false
      } else if (!(this.tail.length == 0 || (this.tail.charCodeAt(0) >= "a".charCodeAt(0) && this.tail.charCodeAt(0) <= "z".charCodeAt(0))
        || (this.tail.charCodeAt(0) >= "A".charCodeAt(0) && this.tail.charCodeAt(0) <= "Z".charCodeAt(0)))) {
        this.reportError("参数异常：-t参数须为长度为1的英文字母，当前输入非英文字母")
        this.calculating = false
      } else if (!(this.reject.length == 0 || (this.reject.charCodeAt(0) >= "a".charCodeAt(0) && this.reject.charCodeAt(0) <= "z".charCodeAt(0))
        || (this.reject.charCodeAt(0) >= "A".charCodeAt(0) && this.reject.charCodeAt(0) <= "Z".charCodeAt(0)))) {
        this.reportError("参数异常：-j参数须为长度为1的英文字母，当前输入非英文字母")
        this.calculating = false
      } else {
        core.async(
          this.inputText,
          [0, this.loop ? 3 : 1, this.loop ? 3 : 1][this.mode],
          !this.head ? 0 : this.head.charCodeAt(0),
          !this.tail ? 0 : this.tail.charCodeAt(0),
          !this.reject ? 0 : this.reject.charCodeAt(0),
          this.mode === 1,
          (e, d) => {
            if (e) this.reportError(e)
            if (/^WordList-GUI: /.test(d)) {
              if (/^WordList-GUI: CoreError: LOOP/.test(d)) {
                this.reportError("输入非法：检测到单词环")
              } else if (/^WordList-GUI: File Input Error: at least two different words/.test(d)) {
                this.reportError("输入非法：至少输入两个不重复单词")
              } else if (/^WordList-GUI: CoreError: No Chain/.test(d)) {
                this.reportError("运行异常：没有合法单词链")
              } else if (/^WordList-GUI: no solution exists/.test(d)) {
                this.reportError("不存在可行解")
              } else {
                this.reportError(d.substring(14))
              }
            } else {
              this.outputText = d
              this.time = '' + moment().diff(start) + ''
            }
          }
        )
      }
    }


  }
};
</script>

<style lang="scss" scoped>
.v-btn {
  text-transform: none;
}

.v-tab {
  text-transform: none;
}
</style>