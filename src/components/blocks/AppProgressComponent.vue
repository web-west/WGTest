<template>
  <div class="progress" :class="{ active: showInfo }">
    <!-- Заголовок progress -->
    <p v-if="label" class="progress__name">{{ label }}</p>
    <div class="progress__block">
      <!-- Всплывающая подсказка progress bar -->
      <div ref="progress-popover" class="progress__popover">
        <!-- Название и проценты progress bar -->
        <span
          class="progress__popover-title"
          :class="{
            ['progress__popover-title_' + style]: styles.includes(style)
          }"
          :style="customStyle"
        >
          {{ name }} — {{ percent }}
        </span>
        <!-- Значение progress bar -->
        <span class="progress__popover-count">{{ value }} employees</span>
      </div>

      <div ref="progress-bar" class="progress__bar">
        <!-- Список progress bar -->
        <div
          v-for="(item, index) in formattedOptions"
          :key="index"
          class="progress__color"
          :style="item.customStyle"
          :class="{
            ['progress__color_' + item.style]: styles.includes(item.style)
          }"
          @mouseover="hoverProgressBlock($event, item), (showInfo = true)"
          @mouseout="showInfo = false"
        ></div>
      </div>
    </div>
    <div class="overlay"></div>
  </div>
</template>

<script>
export default {
  props: {
    /** Данные для заполнения progress */
    options: {
      type: [Object, Array],
      default: null,
      required: true
    },
    /** Название progress */
    label: {
      type: String,
      default: ''
    },
    /** Максимальное значение progress */
    max: {
      type: Number,
      default: 100
    }
  },
  data () {
    return {
      /** Стандартный массив стилей */
      styles: ['low', 'middle', 'high', 'none'],

      /** Контроль видимости подсказки */
      showInfo: false,

      /** Значение процентов для подсказки */
      percent: '',

      /** Значение количества для подсказки */
      value: '',

      /** Значение названия для подсказки */
      name: '',

      /** Значение стиля для подсказки */
      style: '',

      /** Переменная для пользовательских стилей progress bar */
      customStyle: {}
    }
  },
  computed: {
    /** Вычисляем данных для progress bar (проценты, значение Not Rated, рандомный цвет) */
    formattedOptions () {
      let localSum = 0
      /** Переобразрвание данных для progress bar */
      let formatOptions = this.options.map(el => {
        localSum += el.value
        const obj = {
          ...el,
          customStyle: {
            width: (el.value / this.max) * 100 + '%'
          },
          percent: (el.value / this.max) * 100 + '%',
          value: el.value
        }

        /**
         * В случае когда поле стиля пустое будет сгенерирован рандомный цвет progress bar
         */
        const randomColor = Math.floor(Math.random() * 16777215).toString(16)

        if (!this.styles.includes(el.style)) {
          obj.customStyle.backgroundColor = !el.style
            ? '#' + randomColor
            : el.style
        }

        return obj
      })

      /** Вычисление поля Not Rated */
      if (localSum < this.max) {
        formatOptions.push({
          style: 'none',
          name: 'Not Rated',
          customStyle: {
            width: ((this.max - localSum) / this.max) * 100 + '%'
          },
          percent: ((this.max - localSum) / this.max) * 100 + '%',
          value: this.max - localSum
        })
      }
      return formatOptions
    }
  },
  methods: {
    /**
     * Метод формирования данных и стилей для всплывающей подсказки.
     * Отдает заголовок, проценты, значение, стиль для выбранного progress bar.
     * @param {Event} e - Дескриптор события.
     * @param {Object} item - Данные progress bar.
     */
    hoverProgressBlock (e, { percent, style, customStyle, name, value }) {
      this.percent = percent
      this.style = style
      /** Определение стиля для всплывающей подсказки */
      if (!this.styles.includes(style)) {
        this.customStyle.color = style || customStyle.backgroundColor
      } else {
        delete this.customStyle.color
      }
      this.name = name
      this.value = value

      let position = e.target.offsetLeft
      let popover = this.$refs['progress-popover']

      const progressItem = this.$refs['progress-bar']
      const progressPopoverWidth = popover.offsetWidth
      const progressColorWidth = e.target.offsetWidth

      /** Позиционирование всплывающей подсказки */
      if (position + progressPopoverWidth > progressItem.offsetWidth) {
        popover.style.left = position + progressColorWidth + 'px'
        popover.style.transform = 'translate(-100%, -50%)'
      } else {
        popover.style.left = position + 'px'
        popover.style.transform = 'translate(0%, -50%)'
      }
    }
  }
}
</script>

<style lang="scss" scoped>
@import '@/assets/style/variables.scss';
.overlay {
  position: absolute;
  opacity: 0;
  pointer-events: none;
  background-color: #fff;
  transition: 0.3s;
  z-index: 10;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
}
.progress {
  display: flex;
  flex-wrap: wrap;
  align-items: center;
  max-width: 20px 0;
  text-align: left;
  &.active {
    .progress {
      &__popover {
        opacity: 1;
        z-index: 11;
      }
      &__color_none {
        background: $grey;
      }
      &__name {
        z-index: 11;
      }
    }
    .overlay {
      opacity: 0.8;
    }
  }
  &__block {
    position: relative;
  }
  &__name {
    margin-right: 30px;
    min-width: 120px;
    font-size: 16px;
  }
  &__bar {
    display: flex;
    overflow: hidden;
    width: 274px;
    height: 6px;
    border-radius: 4px;
    background: $none;
    position: relative;
  }
  &__color {
    height: 100%;
    transition: 0.3s;
    min-width: 8px;
    cursor: pointer;
    &:hover {
      z-index: 11;
    }
    &_low {
      background: $low;
    }
    &_middle {
      background: $middle;
    }
    &_high {
      background: $high;
    }
    &_none {
      background: $none;
    }
    &:not(:last-child) {
      margin-right: 1px;
    }
  }
  &__popover {
    white-space: nowrap;
    opacity: 0;
    transition: 0.3s;
    position: absolute;
    display: flex;
    flex-direction: column;
    gap: 16px;
    top: 50%;
    transform: translateY(-50%);
    &-title {
      display: block;
      font-weight: bold;
      &_low {
        color: $low;
      }
      &_middle {
        color: $middle;
      }
      &_high {
        color: $high;
      }
      &_none {
        color: $grey;
      }
    }
  }
}
</style>
