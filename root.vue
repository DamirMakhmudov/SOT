
const { createApp, ref, reactive, computed, watch, onMounted, watchEffect, onBeforeUnmount } = Vue;
// const { useQuasar, Loading, QSpinnerGears } = Quasar;

var vueObject = {
  name: "root",
  template:
    /*html*/
    `
    <h1>СОТ</h1>
 
    <div class="container m-1">
 
      <div class="row justify-content-center align-items-center">
        <!-- <div class="row row-cols-3"> -->
        <div class="col">
          <div class="mb-3">
            <label for="" class="form-label">Name</label>
            <input type="text" class="form-control" name="" id="" aria-describedby="helpId" placeholder=""
              v-model="mega.name">
            <small id="helpId" class="form-text text-muted">Help text</small>
          </div>
        </div>
      </div>
 
      <button class="btn btn-primary" @click="someAction()">Notifications</button>
 
    </div>
 
    <!-- <div class="container">
      <div class="row justify-content-center align-items-center g-2">
        <div class="col">Column</div>
        <div class="col">Column</div>
        <div class="col">Column</div>
      </div>
    </div>
 
    <div class="container text-center">
      <div class="row">
        <div class="col">
          1 of 2
        </div>
        <div class="col">
          2 of 2
        </div>
      </div>
      <div class="row">
        <div class="col">
          1 of 3
        </div>
        <div class="col">
          2 of 3
        </div>
        <div class="col">
          3 of 3
        </div>
      </div>
    </div> -->
    `,
  setup() {
    var message = ref("text")
    var mega = reactive({
      name: model.name
    })

    function someAction() {
      console.log('ddd')
    }

    return {
      message,
      mega,
      someAction
    }
  }
}

const app = Vue.createApp(vueObject);

// app.use(Quasar, {
//   config: {
//     notify: { /* look at QuasarConfOptions from the API card */ },
//     loading: { /* look at QuasarConfOptions from the API card */ }
//   }
// });

// Quasar.lang.set(Quasar.lang.ru);