<template>
  <div class="task card shadow bg-ltGreen col-md-11 d-flex flex-direction-column my-4 ml-1">
    <div class="row align-items-center justify-content-between">
      <div class="col-7 col-md-9 card-body">
        <h5 class="card-text">
          {{ taskProp.title }}
        </h5>
      </div>
      <div class="col-1 col-md-2">
        <!-- Button trigger modal -->
        <i class="far fa-comment-dots hvr-raise text-info" type="button" data-toggle="modal" data-target="#commentModal" @click="setTaskId"></i>
      </div>
      <div class="col-1 col-md-1">
        <i class="fas fa-times text-danger hvr-raise" @click="deleteTask"></i>
      </div>
    </div>
    <div class="row">
      <div class="col-md-12">
        <CommentComponent v-for="Comment in state.comment" :key="Comment.id" :comment-prop="Comment" />
      </div>
    </div>
  </div>
</template>

<script>
import { reactive, computed, onMounted } from 'vue'
import { AppState } from '../AppState'
import Notification from '../utils/Notification'
import { tasksService } from '../services/TasksService'
import { commentsService } from '../services/CommentsService'

export default {
  name: 'TaskComponent',
  props: {
    taskProp: {
      type: Object,
      required: true
    }
  },
  setup(props) {
    const state = reactive({
      newComment: {
        taskId: props.taskProp.id
      },
      board: computed(() => AppState.boards),
      list: computed(() => AppState.lists),
      task: computed(() => AppState.tasks),
      comment: computed(() => AppState.comments[props.taskProp.id])
    })

    onMounted(async() => {
      try {
        await tasksService.getAllTasksByListId(props.taskProp.id)
        await commentsService.getAllCommentsByTaskId(props.taskProp.id)
      } catch (error) {
        Notification.toast('connot get all tasks by ListId', 'error')
      }
    })
    return {
      state,

      setTaskId() {
        tasksService.setTaskId(props.taskProp.id)
      },

      async deleteTask() {
        try {
          await tasksService.deleteTask(props.taskProp)
          Notification.toast('Task Deleted')
        } catch (error) {
          Notification.toast('Error: ' + error, 'error')
        }
      }
    }
  },
  components: {}
}
</script>

<style lang="scss" scoped>
.bg-ltGreen{
background-color: rgb(85, 138, 85);
}
</style>
