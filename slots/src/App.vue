  <template>
  <div id="app">
    <ContainerElement>
      <ChatWindowElement @send-msg="post_msg">
        <ChatMessageElement v-for="el in messages" :username="el['author']" :time="el['time']" :key="el['id']">{{el['text']}}</ChatMessageElement>
      </ChatWindowElement>
    </ContainerElement>
  </div>
</template>

<script>
/* eslint-disable */
import ChatWindowElement from './components/ChatWindow.vue'
import ContainerElement from './components/Container.vue'
import ChatMessageElement from './components/ChatMessage.vue'
import './main.js'

export default {
  name: 'App',
  components: {
    ChatWindowElement,
    ContainerElement,
    ChatMessageElement
  },
  data() {
    return {
      messages: []
    }
  },
  methods: {
    convertDate(dateString) {
      const date = new Date(dateString);
      const day = date.getDate();
      const month = date.getMonth() + 1;
      const year = date.getFullYear();
      const hours = date.getHours();
      const minutes = date.getMinutes();
      const seconds = date.getSeconds();

      const formattedDate = `${day < 10 ? '0' : ''}${day}.${month < 10 ? '0' : ''}${month}.${year} ${hours < 10 ? '0' : ''}${hours}:${minutes < 10 ? '0' : ''}${minutes}:${seconds < 10 ? '0' : ''}${seconds}`;
      return formattedDate;
    },
    get_msg() {
      this.axios.get("https://657483b1b2fbb8f6509c4953.mockapi.io/chat/messages").then((res) => {
        let msgs = res['data'];
        this.messages = [];
        msgs.forEach(el => {
          this.messages.push({
            'author': el['author'],
            'time': this.convertDate(el['datetime']),
            'text': el['text']
          })
        });
      });
    },
    post_msg(msg){
      this.axios({
        method: 'post',
        url: 'https://657483b1b2fbb8f6509c4953.mockapi.io/chat/messages',
        data: {
          author: msg.name,
          text: msg.msg
        }
      }).then((res) => { this.get_msg(); })
    }
  },
  mounted(){
    this.get_msg();
    setInterval(this.get_msg, 3000); // через какое-то время этот сервер всё равно говорит, что слишком много у нас запросов, и в принципе, это можно отключить
  }
}
</script>

<style>
body {
  margin: 0;
  background-color: #f9f9fa;
}
</style>
