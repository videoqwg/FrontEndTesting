<template>
  <div class="app-container">
    <div v-if="user">
      <el-row :gutter="20">
        <el-col :span="6" :xs="24">
          <user-card :user="user" />
        </el-col>
        <el-col :span="12" :xs="24">

          <div class="chat-container">
            <!-- 标题区域 -->
            <el-header height="50px" class="chat-header">
              <div class="chat-title">
                <el-badge v-if="unreadCount > 0" is-dot>
                  <span class="chat-title-text">{{ chatTitle }}</span>
                </el-badge>
                <span v-else class="chat-title-text">{{ chatTitle }}</span>
              </div>
              <el-button
                class="refresh-btn"
                icon="el-icon-refresh-right"
                circle
                type="primary"
                size="mini"
                @click="loadMessages"
              />
            </el-header>

            <!-- 消息列表区域 -->
            <el-main ref="chatMain" class="chat-main">
              <el-scrollbar class="chat-scrollbar">
                <div class="chat-message-list">
                  <div
                    v-for="message in messages"
                    :key="message.id"
                    :class="['chat-message-item', message.sender === userId ? 'is-self' : 'is-other']"
                  >
                    <div class="chat-message-info">
                      <span class="chat-message-sender">{{ getSenderName(message.sender) }}</span>
                      <span class="chat-message-time">{{ formatTime(message.time) }}</span>
                    </div>
                    <div class="chat-message-bubble">
                      <p>{{ message.content }}</p>
                    </div>
                  </div>
                </div>
              </el-scrollbar>
            </el-main>

            <!-- 输入区域 -->
            <el-footer height="60px" class="chat-footer">
              <el-input
                v-model="inputContent"
                placeholder="请输入消息..."
                clearable
                @keyup.enter.native="handleSend"
              />
              <el-button type="primary" icon="el-icon-chat-line-round" style="margin-left: 5px" @click="handleSend">
                发送
              </el-button>
            </el-footer>
          </div>
        </el-col>
        <el-col :span="6" :xs="24">
          <user-card :user="user" />
        </el-col>
      </el-row>
    </div>
  </div>
</template>

<script>
import { mapGetters } from 'vuex'
import UserCard from '@/views/profile/components/UserCard'
// import axios from 'axios'
export default {
  name: 'Chat',
  components: { UserCard },
  data() {
    return {
      chatTitle: '与张三的对话',
      userId: 'user_001', // 当前用户的ID，用以区分消息发送者
      messages: [], // 聊天消息列表
      inputContent: '', // 输入框内容
      unreadCount: 0, // 未读消息数，可根据业务场景自行维护
      user: {},
      activeTab: 'activity'
    }
  },
  computed: {
    ...mapGetters([
      'name',
      'avatar',
      'roles',
      'phone',
      'email',
      'introduction'
    ])
  },
  created() {
    this.getUser()
  },
  mounted() {
    this.loadMessages()
  },
  methods: {
    getUser() {
      this.user = {
        name: this.name,
        role: this.roles.join(' | '),
        phone: this.phone,
        email: this.email,
        avatar: this.avatar,
        introduction: this.introduction
      }
    },
    // 加载历史消息
    async loadMessages() {
      try {
        // 在真实环境中，这里通过 axios 请求后端接口，如:
        // const res = await axios.get('/api/chat/messages', { params: { sessionId: xx } });
        // this.messages = res.data;
        // 这里先使用模拟数据
        const mockData = [
          {
            id: 1,
            sender: 'user_002',
            content: '你好，这里是测试消息 1',
            time: '2025-01-01 10:00:00'
          },
          {
            id: 2,
            sender: 'user_001', // 我方用户
            content: '好的，我已收到测试消息。',
            time: '2025-01-01 10:01:00'
          },
          {
            id: 3,
            sender: 'user_002',
            content: '再给你发一条消息试试',
            time: '2025-01-01 10:02:00'
          }
        ]
        this.messages = mockData
        this.scrollToBottom()
      } catch (error) {
        console.error(error)
      }
    },

    // 发送消息
    async handleSend() {
      if (!this.inputContent.trim()) return
      const newMessage = {
        id: Date.now(),
        sender: this.userId,
        content: this.inputContent,
        time: new Date().toLocaleString()
      }
      // 先更新到本地
      this.messages.push(newMessage)
      this.scrollToBottom()

      // 清空输入框
      // const contentToSend = this.inputContent
      this.inputContent = ''

      try {
        // 调用后端接口发送消息，如:
        // await axios.post('/api/chat/send', {
        //   sessionId: xx,
        //   content: contentToSend,
        // });
        // 这里暂时仅做 mock，不做实际的发送
      } catch (error) {
        console.error(error)
      }
    },

    // 滚动到聊天底部
    scrollToBottom() {
      this.$nextTick(() => {
        if (this.$refs.chatMain) {
          const chatMain = this.$refs.chatMain.$el || this.$refs.chatMain
          chatMain.scrollTop = chatMain.scrollHeight
        }
      })
    },

    // 显示发送者的名称（可根据实际逻辑区分）
    getSenderName(senderId) {
      if (senderId === this.userId) {
        return '我'
      }
      // 也可以在此根据 senderId 查询用户名称
      return '张三'
    },

    // 格式化时间
    formatTime(time) {
      // 这里只做简单展示
      return time
    }
  }
}
</script>

  <style scoped>
  .chat-container {
    display: flex;
    flex-direction: column;
    height: 600px; /* 自行调整合适高度 */
    border: 1px solid #dcdcdc;
    border-radius: 12px; /* 整体圆角 */
    overflow: hidden;
  }

  /* 头部样式 */
  .chat-header {
    display: flex;
    align-items: center;
    background: #409eff;
    color: #fff;
  }

  .chat-title {
    display: flex;
    align-items: center;
    justify-content: flex-start;
    margin-left: 10px;
  }

  .chat-title-text {
    font-size: 16px;
  }

  .refresh-btn {
    margin-left: auto;
    margin-right: 10px;
  }

  /* 内容区样式 */
  .chat-main {
    background: #f5f7fa;
    padding: 10px;
    overflow-y: auto;
    flex: 1;
  }

  .chat-scrollbar {
    max-height: 100%;
    height: 100%;
  }

  .chat-message-list {
    display: flex;
    flex-direction: column;
    justify-content: flex-start;
  }

  /* 单条消息 */
  .chat-message-item {
    margin-bottom: 10px;
    display: flex;
    flex-direction: column;
    align-items: flex-start;
  }

  /* 我方用户 */
  .is-self {
    align-items: flex-end;
  }

  .is-self .chat-message-bubble {
    background-color: #409eff;
    color: #fff;
    text-align: left;
  }

  .is-other .chat-message-bubble {
    background-color: #ebeef5;
    color: #333;
    text-align: left;
  }

  /* 消息头部信息 */
  .chat-message-info {
    font-size: 12px;
    color: #909399;
    margin-bottom: 3px;
  }

  .chat-message-sender {
    margin-right: 5px;
  }

  /* 消息气泡：圆角矩形 */
  .chat-message-bubble {
    border-radius: 16px;
    padding: 0px 10px; /* 内边距调整 */
    max-width: 60%;
    word-wrap: break-word;
    line-height: 1; /* 行高调整 */
}

/* 输入区样式 */
.chat-footer {
  display: flex;
  align-items: center;
  padding: 5px 10px;
  background: #fff;
  border-top: 1px solid #ebeef5;
  border-bottom-left-radius: 12px;
  border-bottom-right-radius: 12px;
}
  </style>
