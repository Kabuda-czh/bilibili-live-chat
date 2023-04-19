<template>
  <div class="danmaku-item" :class="{ hidden: isHidden }">
    <img v-if="showFace" class="danmaku-author-face" :src="face" />
    <div v-if="type === 'message'" class="danmaku-content">
      <span class="danmaku-author-name with-colon" :class="{ anchor: isAnchor, owner: isOwner }">{{ uname }}</span>
      <span class="danmaku-message">{{ message }}</span>
    </div>
    <div v-else-if="type === 'gift'" class="danmaku-content">
      <span class="danmaku-message">感谢&nbsp;</span>
      <span class="danmaku-author-name">{{ uname }}</span>
      <span class="danmaku-message">&nbsp;赠送&nbsp;</span>
      <span class="danmaku-gift-name">{{ giftName }}</span>
      <span class="danmaku-message">&nbsp;×&nbsp;</span>
      <span class="danmaku-gift-num">{{ num }}</span>
    </div>
    <div v-else-if="type === 'info'" class="danmaku-content">
      <span class="danmaku-message">{{ message }}</span>
    </div>
  </div>
  <div v-if="type === 'sc'" class="danmaku-sc" :class="{ hidden: scHidden }">
    <div class="danmaku-sc-card">
      <div class="danmaku-sc-content">
        <div class="danmaku-sc-content-div">
          <img v-if="showFace" class="danmaku-sc-author-face" :src="face" />
          <div class="danmaku-content">
            <span class="danmaku-sc-author-name with-colon" :class="{ anchor: isAnchor, owner: isOwner }">{{
              uname
            }}</span>
            <span class="danmaku-sc-message">{{ message }}</span>
          </div>
        </div>
      </div>
      <div class="danmaku-sc-progress">
        <div class="loading" />
        <div class="danmaku-sc-progress-new">
          <span>NEW</span>
        </div>
        <div class="danmaku-sc-progress-click">
          <span>点击回复此条超级留言</span>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { toRefs, ref, computed, onBeforeUnmount } from 'vue';

export default {
  props: {
    type: {
      type: String,
      default: 'message',
    },
    showFace: Boolean,
    face: String,
    uid: Number,
    uname: String,
    message: String,
    isAnchor: Boolean,
    isOwner: Boolean,
    action: String,
    giftName: String,
    num: Number,
    stay: Number,
    hidden: Boolean,
  },
  setup(props) {
    const hidden = ref(false);
    const scHiddenRef = ref(false);
    const needRemoved = ref(false);
    if (props.stay) {
      const stayTimeout = setTimeout(() => {
        hidden.value = true;
        if (props.type === 'info') {
          setTimeout(() => (needRemoved.value = true), 800);
        }
      }, props.stay);
      onBeforeUnmount(() => clearTimeout(stayTimeout));
    }

    setTimeout(() => {
      scHiddenRef.value = true;
    }, 15 * 1000);

    const isHidden = computed(() => props.hidden || hidden.value);
    const scHidden = computed(() => scHiddenRef.value);
    return { ...toRefs(props), isHidden, scHidden, needRemoved };
  },
};
</script>

<style lang="scss">
@font-face {
  font-family: 'HarmonyOS Sans SC';
  src: url(../assets/font/HarmonyOS_Sans_SC_Medium.woff) format('woff');
}

@keyframes danmakuIn {
  from {
    transform: translateX(30px);
    opacity: 0;
  }
  to {
    transform: translateX(0);
    opacity: 1;
  }
}

@keyframes scIn {
  from {
    transform: translateY(150px);
    opacity: 0;
  }
  to {
    transform: translateY(0);
    opacity: 1;
  }
}

@keyframes loading {
  from {
    transform: translateX(0);
  }
  to {
    transform: translateX(-300px);
  }
}

.loading {
  --background-color: #4c92f2;
  --front-color: #3c86e7;

  position: absolute;

  animation: loading 15s linear forwards;

  z-index: 1;

  background-color: var(--background-color);
  background-image: linear-gradient(
    120deg,
    var(--front-color) 35%,
    transparent 40%,
    transparent 55%,
    var(--front-color) 40%
  );
  background-repeat: repeat;
  background-size: 20px auto;
  overflow: hidden;
  width: 100%;
  height: 30px;
}

.danmaku {
  &-item {
    // width: 300px;
    margin-left: 20px;
    padding: 4px;
    display: flex;
    flex-direction: row;
    align-items: flex-start;
    transition: opacity 0.5s;
    margin-bottom: 20px;
    user-select: none;
    animation: 0.4s danmakuIn;
    opacity: 1;
    &.hidden {
      opacity: 0;
    }

    span {
      word-wrap: break-word;
    }
  }
  &-author-face {
    width: 36px;
    height: 36px;
    border-radius: 36px;
    margin-right: 10px;
    display: inline-block;
    pointer-events: none;
  }
  &-content {
    overflow: initial;
    align-self: center;
  }
  &-author-name {
    color: #a0a0a0;
    &.with-colon::after {
      content: '：';
      margin-left: 2px;
    }
    // 主播
    &.anchor {
      color: #fff248;
    }
    // 房管
    &.owner {
      color: #ff9800;
    }
  }
  &-message,
  &-gift-num {
    color: #000;
  }
  &-gift-name {
    color: #eb76ff;
  }
  &-message {
    font-family: 'HarmonyOS Sans SC';
    font-size: 20px;
    line-height: 20px;
  }
  &-gift-num {
    font-family: 'HarmonyOS Sans SC';
    font-size: 20px;
    line-height: 20px;
  }
  &-author-name,
  &-gift-name {
    font-family: 'HarmonyOS Sans SC';
    font-size: 20px;
    line-height: 20px;
  }

  &-sc {
    position: absolute;
    bottom: 0;
    left: 0;
    right: 0;

    animation: 0.4s scIn;
    transition: opacity 0.5s;

    &.hidden {
      opacity: 0;
    }

    span {
      word-wrap: break-word;
      font-family: 'HarmonyOS Sans SC';
    }

    &-card {
      display: flex;
      justify-content: space-between;
      // align-items: center;
      border: 2px solid #f4fdfa;
      flex-direction: column;
      border-radius: 10px;

      margin-bottom: 5px;
      margin-left: 5px;
      margin-right: 5px;
      user-select: none;

      background: #5da2ff;

      // width: 400px;
      height: 120px;

      opacity: 1;

      overflow: hidden;
    }

    &-content {
      width: 100%;

      &-div {
        display: flex;
        height: 100%;
        align-items: center;
        // justify-content: center;
        padding: 10px;
      }
    }

    &-progress {
      width: 100%;
      height: 30px;
      display: flex;
      align-items: center;
      border-bottom-left-radius: 10px;
      border-bottom-right-radius: 10px;
      background: #2869bb;

      position: relative;

      overflow: hidden;

      &-new {
        margin-left: 15px;
        border-radius: 15px;
        background: #f46867;
        padding: 0 2px;

        z-index: 3;

        span {
          color: #fff;
          font-size: 14px;
          // line-height: 14px;
        }
      }

      &-click {
        margin-left: 6px;

        z-index: 3;

        span {
          font-size: 14px;
          line-height: 14px;
          color: #93c8ff;
          font-style: italic;
          font-weight: 300;
        }
      }
    }

    &-author-face {
      width: 36px;
      height: 36px;
      border-radius: 36px;
      margin-right: 10px;
      display: inline-block;
      pointer-events: none;

      border: 4px solid #fff;
      box-shadow: 0 0 2px 2px #000 inset;
    }

    &-author-name {
      color: #b6d7ff;
      &.with-colon::after {
        content: '：';
        margin-left: 2px;
      }
    }

    &-message {
      font-family: 'HarmonyOS Sans SC';
      font-size: 18px;
      line-height: 18px;
      color: #fff;
    }
  }
}
</style>
