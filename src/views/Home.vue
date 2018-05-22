<template>
  <div class="home">
    <!-- 대화 가능 화면 -->
    <div
      v-if="currentUser.isIn"
      class="speech-conference"
    >
      <div>
        <strong>{{ currentUser.name }}</strong>
      </div>
      <input
        type="button"
        @mousedown="onStartSpeech"
        @mouseup="onStopSpeech"
        :value="currentUser.isSpeaking ? '놓아서 멈추세요' : '눌러서 시작하세요'">
      <br>
      <input type="button" @click="leaveRoom" value="나가기">
      <div>
        <div
          v-for="(sentence, index) in sentences"
          :key="index"
        >
          {{ sentence }}
        </div>
      </div>
    </div>
    <!-- 진입 화면 -->
    <div
      v-else
      class="form-join-room"
    >
      <form @submit.prevent="joinRoom">
        <input
          type="text"
          placeholder="이름을 입력하세요"
          required
          v-model="newName"
        >
        <input type="submit">
      </form>
    </div>
  </div>
</template>

<script>
export default {
  name: 'home',
  data () {
    return {
      isSupportAnnyang: false,
      newName: 'John Doe',
      currentUser: {
        name: null,
        isIn: false,
        isSpeaking: false
      },
      sentences: []
    }
  },
  mounted () {
    /* global annyang */
    this.isSupportAnnyang = !!annyang
    if (this.isSupportAnnyang) {
      annyang.setLanguage('ko')
      annyang.addCallback('start', this.startSpeech)
      annyang.addCallback('end', this.endSpeech)
      annyang.addCallback('result', this.processResultSpeech)
    }
  },
  beforeDestroy () {
    if (annyang) {
      annyang.removeCallback()
    }
  },
  methods: {
    startSpeech () {
      this.currentUser.isSpeaking = true
    },
    endSpeech () {
      this.currentUser.isSpeaking = false
    },
    processResultSpeech (resultText) {
      console.log(resultText)
    },
    onStartSpeech () {
      if (this.isSupportAnnyang) {
        annyang.start({ autoRestart: false, paused: true })
      }
    },
    onStopSpeech () {
      if (this.isSupportAnnyang) {
        annyang.abort()
      }
    },
    leaveRoom () {
      this.currentUser.name = ''
      this.currentUser.isIn = false
      this.currentUser.isSpeaking = false
      this.newName = ''
    },
    joinRoom () {
      this.currentUser.name = this.newName
      this.currentUser.isIn = true
    }
  }
}
</script>
