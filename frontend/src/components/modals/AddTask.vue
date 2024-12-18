<template>
  <div class="container">
    <button class="btn btn-success" @click="handleShow">Add Task +</button>

    <div v-if="show" class="modal" tabindex="-1" role="dialog" style="display: block">
      <div class="modal-dialog modal-lg" role="document">
        <div class="modal-content">
          <div class="modal-header">
            <h5 class="modal-title">Add Task</h5>
            <button type="button" class="close" @click="handleClose" aria-label="Close">
              <span aria-hidden="true">&times;</span>
            </button>
          </div>
          <div class="modal-body">
            <form>
              <div class="form-group row">
                <label for="title" class="col-sm-2 col-form-label">Title</label>
                <div class="col-sm-10">
                  <input v-model="title" type="text" class="form-control" :class="{'is-invalid':!isValidTitle && isTouched}" id="title" title="title">
                  <div v-if="!isValidTitle && isTouched" class="invalid-feedback">Title is required and must be at least 3 characters.</div>
                </div>
              </div>
              <div class="form-group row">
                <label for="description" class="col-sm-2 col-form-label">Description</label>
                <div class="col-sm-10">
                  <textarea v-model="description" type="text" class="form-control" id="description"
                    placeholder="Input Description"></textarea>
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
    setReload: Function,
  },
  setup(props) {
    const show = ref(false);
    const title = ref("");
    const description = ref("");
    const status = ref("");
    const isValidTitle = ref(true);
    const isTouched = ref(false);
    const handleClose = () => {
      show.value = false;
      clearFields();
    };

    const handleShow = () => {
      show.value = true;
    };

    const handleSubmit = async () => {
      // validate
      validateTitle();
      if (!isValidTitle.value) {
        console.log("validation failed: title is invalid")
        return;
      }
      try {
        const token = JSON.parse(localStorage.getItem("userInfo") || "").token;
        await axios.post(
          `${process.env.VUE_APP_API_URL}/api/task`,
          {
            title: title.value,
            description: description.value,
            status: "0",
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
    const validateTitle = () => {
      isTouched.value = true;
      isValidTitle.value = title.value.trim().length >= 3;
    }

    const clearFields = () => {
      title.value = "";
      description.value = "";
      status.value = "";
      isValidTitle.value = true;
      isTouched.value = false;
    };

    return {
      show,
      title,
      description,
      status,
      handleClose,
      handleShow,
      handleSubmit,
      isTouched,
      isValidTitle,
    };
  },
});
</script>
