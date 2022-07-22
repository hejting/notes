### el-tooltip在文本超出区域后浮现

+ 插槽 ToolTip.vue

  ```vue
  <template>
    <div class="tool-tip-box">
      <el-tooltip ref="tooltip"
                  :content="tooltipContent"
                  placement="top-start" />
      <div @mouseenter="handleDivMouseEnter($event)"
           @mouseleave="handleDivMouseleave($event)">
        <slot></slot>
      </div>
    </div>
  </template>
  <script>
  import { getStyle } from '@/utils/dom.js'
  import { debounce } from 'throttle-debounce/esm/index'
  
  export default {
    name: 'ToolTip',
    data () {
      return {
        tooltipContent: ''
      }
    },
    created () {
      this.activateTooltip = debounce(50, tooltip => tooltip.handleShowPopper())
    },
    methods: {
      handleDivMouseEnter (event) {
        const target = event.target
        // 判断是否text-overflow, 如果是就显示tooltip
        // 此处可写多个class，入此处的acls、bcls
        let child = target.querySelector('.acls')
        // 如果宽度被限定，则也可以通过高度判断
        let heightFlag = false
        if (!child) {
          child = target.querySelector('.bcls')
          if (!child) {
            // 当前的class
            child = target.querySelector('.tool-tip-class')
            if (child && child.scrollHeight > child.offsetHeight) {
              heightFlag = true
            }
          }
        }
        // use range width instead of scrollWidth to determine whether the text is overflowing
        // to address a potential FireFox bug: https://bugzilla.mozilla.org/show_bug.cgi?id=1074543#c3
        const range = document.createRange()
        range.setStart(child, 0)
        range.setEnd(child, child.childNodes.length)
        const rangeWidth = range.getBoundingClientRect().width
        let padding = (parseInt(getStyle(target, 'paddingLeft'), 10) || 0) +
          (parseInt(getStyle(target, 'paddingRight'), 10) || 0) +
          (parseInt(getStyle(child, 'marginLeft'), 10) || 0)
        if (!padding) {
          padding = 0
        }
        // eslint-disable-next-line no-mixed-operators
        if ((rangeWidth + padding > target.offsetWidth || child.scrollWidth > child.offsetWidth) || heightFlag && this.$refs.tooltip) {
          const tooltip = this.$refs.tooltip
          // TODO 会引起整个 Table 的重新渲染，需要优化
          this.tooltipContent = target.innerText || target.textContent
          tooltip.referenceElm = target
          tooltip.$refs.popper && (tooltip.$refs.popper.style.display = 'none')
          tooltip.doDestroy()
          tooltip.setExpectedState(true)
          this.activateTooltip(tooltip)
        }
      },
      handleDivMouseleave (event) {
        const tooltip = this.$refs.tooltip
        if (tooltip) {
          tooltip.setExpectedState(false)
          tooltip.handleClosePopper()
        }
      }
    }
  }
  </script>
  
  ```

+ 使用（必须包含"tool-tip-class"的class）

  ```vue
  <tool-tip>
  	<div class="name tool-tip-class">{{item.name}}</div>
  </tool-tip>
  ```

+ utils/dom.js

  ```js
  /* istanbul ignore next */
  
  import Vue from 'vue'
  
  const isServer = Vue.prototype.$isServer
  const SPECIAL_CHARS_REGEXP = /([\:\-\_]+(.))/g
  const MOZ_HACK_REGEXP = /^moz([A-Z])/
  const ieVersion = isServer ? 0 : Number(document.documentMode)
  
  /* istanbul ignore next */
  const trim = function(string) {
    return (string || '').replace(/^[\s\uFEFF]+|[\s\uFEFF]+$/g, '')
  }
  /* istanbul ignore next */
  const camelCase = function(name) {
    return name.replace(SPECIAL_CHARS_REGEXP, function(_, separator, letter, offset) {
      return offset ? letter.toUpperCase() : letter
    }).replace(MOZ_HACK_REGEXP, 'Moz$1')
  }
  
  /* istanbul ignore next */
  export const on = (function() {
    if (!isServer && document.addEventListener) {
      return function(element, event, handler) {
        if (element && event && handler) {
          element.addEventListener(event, handler, false)
        }
      }
    } else {
      return function(element, event, handler) {
        if (element && event && handler) {
          element.attachEvent('on' + event, handler)
        }
      }
    }
  })()
  
  /* istanbul ignore next */
  export const off = (function() {
    if (!isServer && document.removeEventListener) {
      return function(element, event, handler) {
        if (element && event) {
          element.removeEventListener(event, handler, false)
        }
      }
    } else {
      return function(element, event, handler) {
        if (element && event) {
          element.detachEvent('on' + event, handler)
        }
      }
    }
  })()
  
  /* istanbul ignore next */
  export const once = function(el, event, fn) {
    var listener = function() {
      if (fn) {
        fn.apply(this, arguments)
      }
      off(el, event, listener)
    }
    on(el, event, listener)
  }
  
  /* istanbul ignore next */
  export function hasClass(el, cls) {
    if (!el || !cls) return false
    if (cls.indexOf(' ') !== -1) throw new Error('className should not contain space.')
    if (el.classList) {
      return el.classList.contains(cls)
    } else {
      return (' ' + el.className + ' ').indexOf(' ' + cls + ' ') > -1
    }
  }
  
  /* istanbul ignore next */
  export function addClass(el, cls) {
    if (!el) return
    var curClass = el.className
    var classes = (cls || '').split(' ')
  
    for (var i = 0, j = classes.length; i < j; i++) {
      var clsName = classes[i]
      if (!clsName) continue
  
      if (el.classList) {
        el.classList.add(clsName)
      } else if (!hasClass(el, clsName)) {
        curClass += ' ' + clsName
      }
    }
    if (!el.classList) {
      el.className = curClass
    }
  }
  
  /* istanbul ignore next */
  export function removeClass(el, cls) {
    if (!el || !cls) return
    var classes = cls.split(' ')
    var curClass = ' ' + el.className + ' '
  
    for (var i = 0, j = classes.length; i < j; i++) {
      var clsName = classes[i]
      if (!clsName) continue
  
      if (el.classList) {
        el.classList.remove(clsName)
      } else if (hasClass(el, clsName)) {
        curClass = curClass.replace(' ' + clsName + ' ', ' ')
      }
    }
    if (!el.classList) {
      el.className = trim(curClass)
    }
  }
  
  /* istanbul ignore next */
  export const getStyle = ieVersion < 9 ? function(element, styleName) {
    if (isServer) return
    if (!element || !styleName) return null
    styleName = camelCase(styleName)
    if (styleName === 'float') {
      styleName = 'styleFloat'
    }
    try {
      switch (styleName) {
        case 'opacity':
          try {
            return element.filters.item('alpha').opacity / 100
          } catch (e) {
            return 1.0
          }
        default:
          return (element.style[styleName] || element.currentStyle ? element.currentStyle[styleName] : null)
      }
    } catch (e) {
      return element.style[styleName]
    }
  } : function(element, styleName) {
    if (isServer) return
    if (!element || !styleName) return null
    styleName = camelCase(styleName)
    if (styleName === 'float') {
      styleName = 'cssFloat'
    }
    try {
      var computed = document.defaultView.getComputedStyle(element, '')
      return element.style[styleName] || computed ? computed[styleName] : null
    } catch (e) {
      return element.style[styleName]
    }
  }
  
  /* istanbul ignore next */
  export function setStyle(element, styleName, value) {
    if (!element || !styleName) return
  
    if (typeof styleName === 'object') {
      for (var prop in styleName) {
        // eslint-disable-next-line no-prototype-builtins
        if (styleName.hasOwnProperty(prop)) {
          setStyle(element, prop, styleName[prop])
        }
      }
    } else {
      styleName = camelCase(styleName)
      if (styleName === 'opacity' && ieVersion < 9) {
        element.style.filter = isNaN(value) ? '' : 'alpha(opacity=' + value * 100 + ')'
      } else {
        element.style[styleName] = value
      }
    }
  }
  
  export const isScroll = (el, vertical) => {
    if (isServer) return
  
    const determinedDirection = vertical !== null || vertical !== undefined
    const overflow = determinedDirection
      ? vertical
        ? getStyle(el, 'overflow-y')
        : getStyle(el, 'overflow-x')
      : getStyle(el, 'overflow')
  
    return overflow.match(/(scroll|auto)/)
  }
  
  export const getScrollContainer = (el, vertical) => {
    if (isServer) return
  
    let parent = el
    while (parent) {
      if ([window, document, document.documentElement].includes(parent)) {
        return window
      }
      if (isScroll(parent, vertical)) {
        return parent
      }
      parent = parent.parentNode
    }
  
    return parent
  }
  
  export const isInContainer = (el, container) => {
    if (isServer || !el || !container) return false
  
    const elRect = el.getBoundingClientRect()
    let containerRect
  
    if ([window, document, document.documentElement, null, undefined].includes(container)) {
      containerRect = {
        top: 0,
        right: window.innerWidth,
        bottom: window.innerHeight,
        left: 0
      }
    } else {
      containerRect = container.getBoundingClientRect()
    }
  
    return elRect.top < containerRect.bottom &&
      elRect.bottom > containerRect.top &&
      elRect.right > containerRect.left &&
      elRect.left < containerRect.right
  }
  ```

+ 下载throttle-debounce

  ```shell
   npm i throttle-debounce@3.0.1 --save
  ```

  

