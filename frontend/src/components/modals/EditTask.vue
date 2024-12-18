<template>
  <div>
    <button class="btn btn-primary" @click="handleShow">Edit</button>

    <div v-if="show" class="modal" tabindex="-1" role="dialog" style="display: block">
      <div class="modal-dialog modal-lg" role="document">
        <div class="modal-content">
          <div class="modal-header">
            <h5 class="modal-title">Edit Task</h5>
            <button type="button" class="close" @click="handleClose" aria-label="Close">
              <span aria-hidden="true">&times;</span>
            </button>
          </div>
          <div class="modal-body">
            <form>
              <div class="form-group row">
                <label for="title" class="col-sm-2 col-form-label">Title:</label>
                <div class="col-sm-10">
                  <input v-model="title" type="text" class="form-control" id="title" title="title">
                </div>
              </div>
              <div class="form-group row">
                <label for="description" class="col-sm-2 col-form-label">Description:</label>
                <div class="col-sm-10">
                  <textarea v-model="description" type="textarea" class="form-control" id="descritpion"
                    title="description"></textarea>
                </div>
              </div>
            </form>
          </div>
          <div class="modal-footer">
            <button type="button" class="btn btn-secondary" @click="handleClose">
              Close
            </button>
            <button type="button" class="btn btn-primary" @click="handleSubmit">
              Save Changes
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref } from "vue";
import axios from "axios";

export default defineComponent({
  props: {
    task: Object,
    setReload: Function,
  },
  setup(props) {
    const show = ref(false);
    const title = ref(props.task?.title);
    const description = ref(props.task?.description);

    const handleClose = () => {
      show.value = false;
    };

    const handleShow = () => {
      show.value = true;
    };

    const handleSubmit = async () => {
      try {
        const token = JSON.parse(localStorage.getItem("userInfo") || "").token;
        await axios.patch(
          `${process.env.VUE_APP_API_URL}/api/task/${props.task?.id}`,
          {
            title: title.value,
            description: description.value,
          },
          {
            headers: {
              Authorization: `Bearer ${token}`,
            },
          }
        );
        if (props.setReload) {
          props.setReload(true);
        }

        handleClose();
      } catch (err) {
        console.error((err as Error).message);
      }
    };

    return {
      show,
      title,
      description,
      handleClose,
      handleShow,
      handleSubmit,
    };


  },
  data() {
    return {
      state: 0, // initial value
    }
  },
});
</script>

<style scoped>
/* Add your styles here */
</style>
