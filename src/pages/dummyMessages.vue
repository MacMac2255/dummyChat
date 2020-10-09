<template>
  <div>
    <div class="row">
      <q-toolbar
        class="shadow-1 col-xs-12 col-sm-8 offset-sm-2 col-md-6 offset-md-3 bg-grey-1"
      >
        <q-btn round>
          <q-avatar>
            <q-badge
              color="green"
              floating
              style="bottom: -6px; top: auto; left: auto; right: auto"
              transparent
              >online
            </q-badge>
          </q-avatar>
        </q-btn>
        <q-toolbar-title>User</q-toolbar-title>
        <q-space></q-space>
      </q-toolbar>
      <q-layout
        class="shadow-1 rounded-borders col-xs-12 col-sm-8 offset-sm-2 col-md-6 offset-md-3"
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
              <q-scroll-observer
                @scroll="scrollHandler"
                ref="scrollObserver"
              ></q-scroll-observer>
              <q-infinite-scroll
                :offset="20"
                :scroll-target="$refs.scrollTargetRef"
                @load="onLoad"
                id="chatArea"
                ref="chatArea"
                reverse
              >
                <template slot="loading">
                  <div class="row justify-center q-my-md">
                    <q-spinner color="primary" name="dots" size="40px" />
                  </div>
                </template>
                <div
                  class="text-center"
                  style="top: calc(50% - 60px); position: absolute; width: 100%"
                  v-if="messages.length === 0 && finishedLoading"
                >
                  <q-avatar size="100px">
                    <q-img
                      src="https://heartrealestate.com.au/wp-content/uploads/2016/09/default-user-img.jpg"
                      :ratio="1"
                    />
                  </q-avatar>
                  <div class="text-body1 text-italic text-bold q-px-md qpt-xs">
                    Say hi
                  </div>
                </div>
                <div
                  :key="index"
                  class="caption q-px-sm"
                  v-for="(item, index) in messages"
                >
                  <q-chat-message
                    bg-color="blue"
                    :id="item.id"
                    name="me"
                    :text="item.message"
                    text-color="white"
                    text-sanitize
                  >
                    <template v-slot:avatar>
                      <q-avatar class="q-ml-xs q-mr-xs">
                        <q-img
                          src="https://heartrealestate.com.au/wp-content/uploads/2016/09/default-user-img.jpg"
                          :ratio="1"
                        />
                      </q-avatar>
                    </template>
                  </q-chat-message>
                </div>
                <q-btn
                  v-if="showScrollToBottom"
                  @click="scrollToBottom"
                  round
                  icon="fas fa-arrow-down"
                  size="md"
                  style="position: absolute; bottom: 6px; left: 0; right: 0; margin: auto;"
                  text-color="primary"
                  color="white"
                />
                <div id="bottomAnchor"></div>
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
              @focus="focusOnInput"
              @focusout="inputIsFocused = false"
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
import { scroll } from "quasar";
const { getScrollTarget, setScrollPosition } = scroll;

function scrollToElement(el, duration = 1000) {
  if (el) {
    const target = getScrollTarget(el);
    if (target) {
      const offset = el.offsetTop;
      setScrollPosition(target, offset, duration);
    }
  }
}

export default {
  name: "Message",
  data() {
    return {
      messages: [],
      prevId: null,
      prevBid: null,
      prevUser: null,
      finishedLoading: false,
      newMessage: "",
      dateNow: new Date(),
      actionMenu: false,
      sending: false,
      joinInterval: null,
      showScrollToBottom: false,
      minimumOffset: null,
      scrollOffset: null,
      inputIsFocused: false,
      meta: {
        last_page: 1
      },
      message: {
        senderId: 1,
        receiverId: 2,
        message: "test",
        type: 1,
        created_at: "2020-02-02"
      },
      addMessages: [
        {
          senderId: 1,
          receiverId: 2,
          message: "test",
          type: 1,
          created_at: "2020-02-02"
        },
        {
          senderId: 2,
          receiverId: 1,
          message: "test",
          type: 1,
          created_at: "2020-02-02"
        },
        {
          senderId: 1,
          receiverId: 2,
          message: "test",
          type: 1,
          created_at: "2020-02-02"
        },
        {
          senderId: 2,
          receiverId: 1,
          message: "test",
          type: 1,
          created_at: "2020-02-02"
        },
        {
          senderId: 1,
          receiverId: 2,
          message: "test",
          type: 1,
          created_at: "2020-02-02"
        },
        {
          senderId: 2,
          receiverId: 1,
          message: "test",
          type: 1,
          created_at: "2020-02-02"
        }
      ]
    };
  },
  computed: {
    chatClass() {
      if (this.$q.platform.is.capacitor) {
        return this.inputIsFocused
          ? "col-12"
          : this.$q.screen.width < 500
          ? "col-10"
          : "col-11";
      } else {
        return this.inputIsFocused
          ? "col-12"
          : this.$q.screen.width < 850
          ? "col-9"
          : "col-10";
      }
    }
  },
  watch: {
    "$q.screen.height"() {
      //recalculate minimum offset
      this.minimumOffset = null;
      this.setScrollToBottom(this.$refs.scrollObserver.getPosition());
    }
  },
  mounted() {
    this.init();
  },
  beforeDestroy() {
    window.removeEventListener("keyboardDidShow", () => {
      this.showScrollToBottom = false;
    });
  },
  methods: {
    addMessage(message) {
      this.messages.push(message);
    },
    focusOnInput() {
      this.inputIsFocused = true;
    },
    init() {
      this.finishedLoading = false;
      this.messages = [];
      this.joinChannel();
    },
    joinChannel() {},
    onLoad(index, done) {
      this.meta.last_page = this.addMessages.length * 10;
      this.addMessages.forEach(addMessage => {
        console.log(addMessage);
        this.addMessage(addMessage);
      });
      done();
      console.log(this.messages);
      this.$refs.chatArea.stop();

      if (this.meta.last_page <= index) {
        if (this.$refs.chatArea) {
        }
      }
      this.finishedLoading = true;
    },
    scrollHandler(details) {
      if (details.position !== 0) {
        this.setScrollToBottom(details);
      }
    },
    scrollToBottom() {
      this.minimumOffset = null;
    },
    sendMessage() {
      this.addMessage(this.newMessage);
      this.newMessage = "";
    },
    setScrollToBottom(details = 0) {
      this.scrollOffset =
        this.$refs.chatArea.$el.scrollHeight - details.position;
      if (!this.minimumOffset || this.scrollOffset < this.minimumOffset) {
        this.minimumOffset = this.scrollOffset;
      }
      this.showScrollToBottom = this.scrollOffset > this.minimumOffset * 2;
    }
  }
};
</script>
<style scoped></style>
