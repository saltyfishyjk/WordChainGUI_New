<template>
  <v-app style="min-height: '800px'">
    <v-main id="main">
      <v-container fluid class="fill-height">
        <div>
          <v-row style="height: 100%">
            <v-col>
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
          </v-row>
        </div>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>


export default {
  name: 'App',
  data: () => ({
    tab: null,
    items: [
      { tab: '-n', content: '单词链数量\n-n' },
      { tab: '-w', content: '<p class="mb-0">Etiam vitae tortor. Curabitur ullamcorper ultricies nisi. Sed magna purus, fermentum eu, tincidunt eu, varius ut, felis. Aliquam lobortis. Suspendisse potenti.</p>' },
      { tab: '-c', content: 'Tab 3 Content' },
      { tab: '-h', content: 'Tab 4 Content' },
      { tab: '-t', content: 'Tab 5 Content' },
      { tab: '-j', content: 'Tab 6 Content' },
      { tab: '-r', content: 'Tab 7 Content' },
    ],
  }),

  methods: {
  }
};
</script>


