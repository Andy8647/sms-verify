<template>
  <div class="enter-profile-wrap">

    <div class="dummy-input">
        <div v-for="i in 6" :id="'code-'+ (i - 1)" @click="focusInput" :key="i+2">
          {{codeList[i-1]}}
        </div>
    </div>

    <input autocomplete="one-time-code" @input="setSMSCode"
           inputmode="numeric" v-model="SMSCode" id="real-input" />

    <div v-if="!isCodeValid" class="code-error-msg">Invalid Code</div>

    <div class="resend-code">
      <span v-if="timer !== 0"> resend {{timer}}s later </span>
      <span v-if="timer === 0" @click="resetTimer" id="resend-btn">resend</span>
    </div>


  </div>
</template>

<script lang="ts">
import { defineComponent, ref, onMounted } from "vue";

export default defineComponent({
  name: "SMSVerify",
  setup(props, {emit}) {
    const codeList = ref<string[]>([" ", " ", " ", " ", " ", " "]);
    const SMSCode = ref<string>("")
    const TIMERLENGTH = 60;
    const isCodeValid = ref<boolean>(true)
    const isCodeFilled = ref<boolean>(false)

    const focusCode = (index: number, option: string): void => {
      const curNode: HTMLElement = document.getElementById('code-'+index)
      switch (option) {
        case "add":
          curNode.classList.add("focus");
          break;
        case "remove":
          curNode.classList.remove("focus");
          break;
      }
    }

    const formatCode = (code: string): string => {
      let temp = ""
      const digit = /^[0-9]*$/
      for (let i = 0; i < code.length; i++) {
        const c = code.charAt(i)
        if (digit.test(c)) {
          temp += c
        }
      }
      return temp
    }
    const setSMSCode = (event): void => {
      // format the input code to only contain digit
      SMSCode.value = formatCode(event.target.value)

      if (event.target.value.length <= 6) {
        codeList.value = SMSCode.value.padEnd(6, " ").split("")
      } else if (SMSCode.value.length === 6){
        // todo: add verify SMS Code serve call and error state and lose focus
      } else {
        SMSCode.value = event.target.value.substr(0, 6)
      }
      isCodeFilled.value = SMSCode.value.length === 6

      for (let i = 0; i < 6; i ++) {
        focusCode(i, "remove")
        if (i === SMSCode.value.length) {
          focusCode(i - 1, "add")
        }
      }
    }

    const timer = ref<number>(TIMERLENGTH)
    const countDown = (): void => {
      const myTimer = setInterval(() => {
        if (timer.value > 0) timer.value --
        else clearInterval(myTimer)
      }, 1000)
    }
    const resetTimer = (): void => {
      timer.value = TIMERLENGTH
      countDown()
    }

    const focusInput = (): void => {
      const realInput: HTMLElement = document.getElementById("real-input")
      realInput.focus()
      focusCode(0, "add")
    }

    onMounted(() => {
      countDown()
    })

    return {
      codeList, setSMSCode, SMSCode, focusInput, timer, resetTimer,
      countDown, isCodeValid, isCodeFilled
    }
  }
})
</script>

<style lang="scss">
$purple: #7269D5;

.enter-profile-wrap {
  width: 85%;
  margin: 0 auto 0 auto;
  border-radius: 1rem;
  box-shadow: 0 0.25rem 0.75rem rgba(0,0,0,0.1);
  display: flex;
  flex-direction: column;
  background-color: rgba(237, 238, 250, 0.7);
  justify-content: center;
  align-items: center;
  line-height: 1.5rem;
  color: $purple;
  font-family: "PingFang SC", sans-serif;
  padding-bottom: 1rem;
}

.dummy-input {
  display: flex;
  margin: 1rem 1rem 0 1rem;
  justify-content: space-between;
  padding: 0 1rem;
  width: 100%;

  div {
    width: 2.75rem;
    height: 2.688rem;
    margin: auto;
    border: none;
    border-radius: 0.3125rem;
    outline: none;
    background-color: #fff;
    color: #333;
    font-weight: 500;
    display: flex;
    justify-content: center;
    align-items: center;
    font-size: 1.5rem;
    line-height: 2rem;
  }
}

#real-input {
  position: absolute;
  top: -999rem;

  // bug on andriod & sogou input
  // width: 0;
  // height: 0;
  // border: none;
}

.resend-code {
  align-self: flex-start;
  margin: 2rem auto auto 1rem;
  font-size: 0.875rem;
  line-height: 1.25rem;
  height: 4.6rem;
}

#resend-btn {
  text-decoration: underline;
}

.focus {
  box-shadow: 0 0 0.25rem 0.2rem rgba(0, 0, 0, 0.1) ;
}
.code-error-msg {
  color: #F03D3E;
  font-size: 0.875rem;
  line-height: 1.25rem;
  align-self: flex-start;
  margin: 0 auto 0 1rem;
}
</style>