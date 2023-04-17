const { createApp, ref, reactive, computed, watch, onMounted, watchEffect, onBeforeUnmount } = Vue;
const { useQuasar, Loading, QSpinnerGears } = Quasar;

var vueObject = {
  name: "root",
  template:
    /*html*/
    `
<h3>СОТ</h3>
<div class="q-pa-md fit column justify-center">
  <div class="q-gutter-y-xs" style="height:220px">

    <q-select v-model="mega.city_id.val" use-input input-debounce="0" label="Тип расхода" :options="mega.filterProfile" behavior="menu">
      <template v-slot:prepend>
        <q-icon name="list" />
      </template>
      <template v-slot:no-option>
        <q-item>
          <q-item-section class="text-grey">
            Не нашел такого, ты уверен(а)?
          </q-item-section>
        </q-item>
      </template>
    </q-select>

  </div>
</div>

`,
  setup() {
    var message = ref("text")
    var mega = reactive({
      city_id: model.city_id.val,
      filterProfile: model.filterProfile
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

app.use(Quasar, {
  config: {
    notify: { /* look at QuasarConfOptions from the API card */ },
    loading: { /* look at QuasarConfOptions from the API card */ }
  }
});

Quasar.lang.set(Quasar.lang.ru);