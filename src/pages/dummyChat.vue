<template>
  <div class="row">
    <div class="col-8 offset-2">
      <q-layout
        class="shadow-1 rounded-borders"
        container
        style="height: calc(100vh - 165px)"
        view="lhh LpR lff"
      >
        <q-page-container>
          <q-page>
            <div
              class="scroll"
              ref="scrollTargetRef"
              style="max-height: calc(100vh - 165px); overflow: auto;"
            >
              <q-infinite-scroll @load="onLoad" reverse>
                <template slot="loading">
                  <div class="row justify-center q-my-md">
                    <q-spinner color="primary" name="dots" size="40px" />
                  </div>
                </template>

                <div
                  v-for="(item, index) in items"
                  :key="index"
                  class="caption q-py-sm"
                >
                  <q-chat-message :text="[item.text]" :sent="item.sent" />
                </div>
              </q-infinite-scroll>
            </div>
          </q-page>
        </q-page-container>
      </q-layout>
    </div>
    <div class="row">
      <q-footer
        class="col-xs-12 col-sm-8 offset-sm-2 col-md-6 offset-md-3 bg-grey-1"
      >
        <div class="row">
          <div class="col-8 offset-2">
            <q-input
              @keyup.enter="sendMessage"
              color="secondary"
              outlined
              placeholder="Send a message..."
              v-model="newMessage"
              ref="inputArea"
            >
              <q-icon
                style="margin:auto; top:0; bottom:0"
                right
                @click="sendMessage()"
                color="primary"
                flat
                name="send"
                round
                size="35px"
              />
            </q-input>
          </div>
        </div>
      </q-footer>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      items: [
        { text: "How're you doin?", sent: true },
        { text: "Doin fin how're you doin?", sent: false },
        { text: "How're you doin?", sent: true },
        { text: "Doin fin how're you doin?", sent: false },
        { text: "How're you doin?", sent: true },
        { text: "Doin fin how're you doin?", sent: false },
        { text: "How're you doin?", sent: true },
        { text: "Doin fin how're you doin?", sent: false },
        { text: "How're you doin?", sent: true },
        { text: "Doin fin how're you doin?", sent: false }
      ],
      newMessage: ""
    };
  },

  methods: {
    onLoad(index, done) {
      setTimeout(() => {
        if (this.items) {
          this.items.splice(
            0,
            0,
            { text: "How're you doin?", sent: true },
            { text: "Doin fin how're you doin?", sent: false },
            { text: "How're you doin?", sent: true },
            { text: "Doin fin how're you doin?", sent: false },
            { text: "How're you doin?", sent: true },
            { text: "Doin fin how're you doin?", sent: false },
            { text: "How're you doin?", sent: true },
            { text: "Doin fin how're you doin?", sent: false },
            { text: "How're you doin?", sent: true },
            { text: "Doin fin how're you doin?", sent: false }
          );
          done();
        }
      }, 2000);
    },
    sendMessage() {
      this.items.push({ text: this.newMessage, sent: true });
      this.newMessage = "";
    }
  }
};
</script>
<style scoped></style>
