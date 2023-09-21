<template>
  <div class="SkeletonKeyboard">
    <!-- 展示候选词 -->
    <div class="candidateBox">
      <span class="tip_box">
        {{ isCN ? '中文输入' : '英文输入' }} ：{{ outData }}
      </span>
      <div class="chinese" v-if="isCN && cnData.length > 0">
        <span @click="hanzi_index--">&lt;</span>
        <div class="chinese_box">
          <span
            @click="pushHanZi(item)"
            v-for="item in show_hanzi"
            v-text="item"
          ></span>
        </div>
        <span @click="hanzi_index++">></span>
      </div>
    </div>
    <!-- 虚拟键盘 -->
    <div class="keys">
      <div class="rows" v-for="item in keys">
        <span
          @click="input_key(key)"
          class="cols"
          v-for="key in item"
          v-text="isCN ? key.toLocaleUpperCase() : key"
        ></span>
      </div>
    </div>
  </div>
</template>

<script>
  import pinyinUtil from '../components/pinyin/pinyinUtil.js'
  export default {
    name: 'SkeletonKeyboard',
    data() {
      return {
        keys: [],
        UpperKeys: [
          [
            '~',
            '!',
            '@',
            '#',
            '$',
            '%',
            '^',
            '&',
            '*',
            '(',
            ')',
            '_',
            '+',
            'DELETE',
          ],
          [
            'TAB',
            'Q',
            'W',
            'E',
            'R',
            'T',
            'Y',
            'U',
            'I',
            'O',
            'P',
            '[',
            ']',
            '|',
          ],
          [
            '小写',
            'A',
            'S',
            'D',
            'F',
            'G',
            'H',
            'J',
            'K',
            'L',
            ':',
            '"',
            '回车',
          ],
          ['中/英', 'Z', 'X', 'C', 'V', 'B', 'N', 'M', '<', '>', '?', '空格'],
        ],
        LowerKeys: [
          [
            '`',
            '1',
            '2',
            '3',
            '4',
            '5',
            '6',
            '7',
            '8',
            '9',
            '0',
            '-',
            '=',
            'DELETE',
          ],
          [
            'TAB',
            'q',
            'w',
            'e',
            'r',
            't',
            'y',
            'u',
            'i',
            'o',
            'p',
            '[',
            ']',
            '\\',
          ],
          [
            '大写',
            'a',
            's',
            'd',
            'f',
            'g',
            'h',
            'j',
            'k',
            'l',
            ';',
            "'",
            '回车',
          ],
          ['中/英', 'z', 'x', 'c', 'v', 'b', 'n', 'm', ',', '.', '/', '空格'],
        ],
        isUpper: false,
        isCN: false,
        outData: '',
        cnData: '',
        hanzi_index: 0,
        show_hanzi: '',
      }
    },

    methods: {
      pinYin(key) {
        if (
          this.outData.length === 0 &&
          '`qwertyuiopasdfghjklzxcvbnm'.indexOf(key.toLocaleLowerCase()) === -1
        ) {
          this.$emit('on-push', key)
          return
        }
        this.outData += key
      },
      pushHanZi(key) {
        if (key === ' ') return
        this.$emit('on-push', key)
        this.outData = ''
      },
      input_key(key) {
        switch (key) {
          case 'DELETE':
          case 'delete':
            if (this.isCN && this.outData.length > 0) {
              this.outData = this.outData.substring(0, this.outData.length - 1)
              break
            }
            this.$emit('on-delete', key)
            break
          case 'TAB':
          case 'tab':
            break
          case '回车':
            break
          case '大写':
          case '小写':
            this.isUpper = !this.isUpper
            break
          case '中/英':
            this.isCN = !this.isCN
            break
          default:
            {
              if (this.isCN) {
                this.pinYin(key)
              } else {
                this.$emit('on-push', key)
              }
            }
            break
        }
      },
      update_Hanzi() {
        // debugger
        let show_hanzi = this.cnData.substring(
          this.hanzi_index * 5,
          (this.hanzi_index + 1) * 5,
        )
        for (let i = show_hanzi.length; i < 5; i++) {
          show_hanzi += ' '
        }
        // console.log(show_hanzi,'22')
        this.show_hanzi = show_hanzi
      },
    },
    watch: {
      isUpper(n) {
        this.keys = n ? this.UpperKeys : this.LowerKeys
      },
      outData(n) {
        this.cnData = pinyinUtil.getHanzi(n)
      },
      cnData(n) {
        this.hanzi_index = 0
        this.update_Hanzi()
      },
      hanzi_index(n, o) {
        if (n < 0) {
          this.hanzi_index = 0
          return
        }
        let m_index = parseInt(((this.cnData.length + 4) / 5).toString())
        if (n !== 0 && n >= m_index) {
          this.hanzi_index = m_index - 1
          return
        }
        this.update_Hanzi()
      },
    },
    created() {
      this.keys = this.LowerKeys
    },
  }
</script>

<style scoped>
  .SkeletonKeyboard {
    width: 100%;
    height: 100%;
    text-align: center;
  }

  .candidateBox {
    box-sizing: border-box;
    margin: 0.3em;
    border-radius: 5px;
    border: 1px solid #ccc;
    height: 2.5em;
    line-height: 1.5em;
  }

  .tip_box {
    float: left;
    padding: 0.5em 0.3em;
  }
  .chinese {
    float: right;
  }
  .chinese_box {
    display: inline-flex;
    width: 12em;
  }

  .rows {
    display: flex;
  }
  .cols {
    flex: 1;
    margin: 0.3em;
    padding: 0.5em;
    box-sizing: border-box;
    border-radius: 3px;
    border: 1px solid #cccccc;
    display: inline-block;
    min-width: 2.5em;
    height: 2.5em;
    line-height: 1.5em;
    cursor: pointer;
    user-select: none;
  }

  .chinese span {
    cursor: pointer;
    border: 1px solid #cccccc;
    box-sizing: border-box;
    padding: 0.1em 0.3em;
    margin: 0.3em;
    border-radius: 4px;
  }
</style>
