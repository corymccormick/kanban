<template>
  <div class="modal fade"
       id="commentModal"
       tabindex="-1"
       role="dialog"
       aria-labelledby="#commentModalLabel"
       aria-hidden="true"
  >
    <div class="modal-dialog" role="document">
      <div class="modal-content">
        <div class="modal-header">
          <h5 class="modal-title" id="commentModalLabel">
            Create Comment
          </h5>
          <button type="button" class="close" data-dismiss="modal" aria-label="Close">
            <span aria-hidden="true">&times;</span>
          </button>
        </div>
        <div class="modal-body">
          <form class="form-inline" @submit.prevent="createComment">
            <div class="form-group m-2">
              <input type="text"
                     class="form-control col-12 col-md-12"
                     id="comment"
                     placeholder="Comment Here..."
                     v-model="state.newComment.body"
              >
              <button type="submit" class="btn btn-primary">
                Post
              </button>
            </div>
            <div class="modal-footer">
              <button type="button" class="btn btn-secondary" data-dismiss="modal">
                Close
              </button>
            </div>
          </form>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { reactive, computed } from 'vue'
import { AppState } from '../AppState'
import Notification from '../utils/Notification'
import { commentsService } from '../services/CommentsService'
import $ from 'jquery'

export default {
  name: 'CommentModal',
  props: {},
  setup(props) {
    const state = reactive({
      newComment: {},
      activeTask: computed(() => AppState.activeTask)
    })

    return {
      state,

      async createComment() {
        try {
          state.newComment.taskId = state.activeTask
          await commentsService.createComment(state.newComment)

          state.newComment = {}
          // eslint-disable-next-line no-undef
          $('#commentModal').modal('hide')
          Notification.toast('Comment Created!', 'success')
          // TODO find new way to clear input field, while NOT throwing away list ID, you dumb donkey
        } catch (error) {
          console.error(error)
          Notification.toast('Comment not created', 'error')
        }
      }
    }
  },
  components: {}
}
</script>

<style>

</style>
