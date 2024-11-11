<template>
  <div class="ads">
    <van-submit-bar
        label="我的钱包"
        :price="Number(moneny) * 100"
        :decimal-length="2"
        button-text="提现"
        @submit="onSubmit"
    />
    <van-pull-refresh v-model="refreshing" @refresh="onRefresh">
      <van-list
          v-model:loading="loading"
          :finished="finished"
          finished-text="没有更多了"
          @load="onLoad"
      >
        <van-cell-group inset center>
          <van-cell
              v-for="(item, index) in list"
              :label="item.des"
          >
            <template #title>
              <p>{{ item.title }} <span style="padding: 10px"> + {{ item.amount }}</span></p>
            </template>
            <!-- 使用 right-icon 插槽来自定义右侧图标 -->
            <template #icon>
              <van-image
                  style="margin-right: 10px"
                  width="40"
                  height="40"
                  src="../assets/images/dy.png"
              />
            </template>
            <!-- 使用 right-icon 插槽来自定义右侧图标 -->
            <template #value>

              <van-button
                  size="small"
                  @click="() => openDialog(index, item)"
                  type="primary"
                  :disabled="item.isFinish"
              >{{item.isFinish?'已完成':'前往'}}
              </van-button
              >
            </template>
          </van-cell>
        </van-cell-group>
      </van-list>
    </van-pull-refresh>
  </div>
</template>

<script lang="tsx">
import {ref, h} from "vue";
import Video from "./video.vue";
import Success from "./success.vue";
import {showDialog} from "vant";

export default {
  setup() {
    const list = ref([]);
    const loading = ref(false);
    const finished = ref(false);
    const refreshing = ref(false);
    const moneny = ref(0);
    const onLoad = () => {
      setTimeout(() => {
        if (refreshing.value) {
          list.value = [];
          refreshing.value = false;
        }

        for (let i = 0; i < 10; i++) {
          list.value.push({
            title: "看视频领红包",
            des: "观看精彩视频即可领红包",
            path: `../assets/images/${i + 1}.mp4`,
            amount: (Math.random() * (2 - 1) + 1).toFixed(2),
          });
        }
        loading.value = false;

        if (list.value.length >= 40) {
          finished.value = true;
        }
      }, 1000);
    };

    const onRefresh = () => {
      // 清空列表数据
      finished.value = false;

      // 重新加载数据
      // 将 loading 设置为 true，表示处于加载状态
      loading.value = true;
      onLoad();
    };

    const timer = ref(10);
    const url = ref("");
    const amount = ref(0);
    const index = ref(null)
    let countdownInterval = null;
    let isTimerComplete = false;

    const openDialog = (index, item) => {
      startCountdown(index, item);

      showDialog({
        title: "观看视频",
        overlay: false,
        className: 'video_dl',
        message: () =>
            h(Video, {
              url: url.value,
              timer: timer.value,
            }),
        showConfirmButton: false,
        showCancelButton: true,
        closeOnClickOverlay: false,
        lockScroll: false,
        cancelButtonText: '关闭',
        beforeClose: () => {
          clearIn(true)
          return true
        }
      }).then(() => {
        clearIn();
      });
    };

    const startCountdown = (i, item) => {
      index.value = Number(i)
      amount.value = item.amount;
      const randomInt = Math.floor(Math.random() * 11) + 1;
      console.log("randomInt", randomInt);
      url.value = `../assets/images/${randomInt}.mp4`;
      countdownInterval = setInterval(() => {
        timer.value -= 1;
        console.log("timer.value", timer.value);
        if (timer.value <= 0) {
          clearIn();
          closeDialog();
          showDialog({
            title: "",
            message: () =>
                h(Success, {
                  amount: amount.value,
                }),
            showConfirmButton: true,
            confirmButtonText: "确认",
            showCancelButton: false,
            closeOnClickOverlay: false,
          }).then(() => {
            moneny.value += Number(amount.value);
            amount.value = 0;
          });
        }
      }, 1000);
    };

    const onSubmit = () => {

      showDialog({
        title: "提现成功！",
        message: () =>
            h(Success, {
              amount: moneny.value,
              isSuccess: true,
            }),
        showConfirmButton: true,
        confirmButtonText: "确认",
        showCancelButton: false,
        closeOnClickOverlay: false,
      }).then(() => {
        moneny.value = 0;
      });
    };

    const clearIn = (isCancel = false) => {
      if (isCancel) {
        clearInterval(countdownInterval);
        timer.value = 10;
        isTimerComplete = true;
        url.value = "";
        showNotify({
          type: "danger",
          message: "为满足时长，无法获取奖励！",
          duration: 1000,
        });
      } else {
        clearInterval(countdownInterval);
        timer.value = 10;
        list.value[index.value]['isFinish'] = true
        isTimerComplete = true;
        url.value = "";
        showNotify({
          type: "success",
          message: "成功获取奖励！",
          duration: 1000,
        });
      }
    };

    return {
      list,
      onLoad,
      loading,
      finished,
      onRefresh,
      refreshing,
      moneny,
      openDialog,
      onSubmit,
    };
  },
};
</script>

<style lang="less">
.van-cell {
  align-items: center;
}

.van-cell__title,
.van-cell__value {
  flex: auto;
}

.van-cell__title {
  p > span {
    color: red;
  }
}

.van-cell__value {
  display: flex;
  align-items: center;
  justify-content: space-around;
}

.video_dl {
  padding: 0;
  background: none;
  .van-dialog__footer{
    width: 50%;
    margin: auto;
  }
  .van-dialog__header {
    display: none;
  }

  .van-dialog__message {
    padding: 0;
    overflow-y: hidden;
  }
}
</style>
