const { createApp, ref, reactive, computed, watch, onMounted, watchEffect, onBeforeUnmount, onBeforeMount } = Vue;
const { useQuasar, Loading, QSpinnerGears } = Quasar;

var vueObject = {
  name: "root",
  template:
    /*html*/
    `
<q-btn color="red" icon="test" class="full-width" @click="ttt()" v-if="mega.showOkbutton"></q-btn>
{{mega.allowSOTsave}}

{{mega.user_email}}

{{mega.date_begin.val}}

<div class="q-pa-md fit column">

  <div class="q-gutter-xs"> <!-- style="height:220px" -->

    <div class="text-h6">Создание СОТ Операторов</div>

    <!-- id_sot_o -->
    <q-input v-model="mega.id_sot_o.val" readonly disable @update:model-value="val => SOT_find()">
      <template v-slot:prepend>
        <q-icon name="123" />
      </template>
    </q-input>

    <!-- city_id -->
    <q-select v-model="mega.city_id.val" use-input input-debounce="0" label="Филиал" :options="mega.Filials"
      behavior="menu" @filter="filterFilials" @update:model-value="val => SOT_find()">
      <template v-slot:prepend>
        <q-icon name="business" />
      </template>
      <template v-slot:no-option>
        <q-item>
          <q-item-section class="text-grey">
            Не нашел такого, ты уверен(а)?
          </q-item-section>
        </q-item>
      </template>
    </q-select>

    <!-- group_id -->
    <q-select v-model="mega.group_id.val" use-input input-debounce="0" label="Группа" :options="mega.Groups"
      behavior="menu" @filter="filterGroups" @update:model-value="val => SOT_find()">
      <template v-slot:prepend>
        <q-icon name="groups" />
      </template>
      <template v-slot:no-option>
        <q-item>
          <q-item-section class="text-grey">
            Не нашел такого, ты уверен(а)?
          </q-item-section>
        </q-item>
      </template>
    </q-select>

    <!-- date -->
    <q-input v-model="mega.date_begin.val" type="date" hint="Native date"  @update:model-value="val => SOT_find()" >
      <template v-slot:prepend>
        <q-icon name="event" />
      </template>
    </q-input>
    <!--
    <q-input v-model="mega.date_begin.val" type="date" @update:model-value="val => SOT_find()" hint="Native date"> 
      <template v-slot:prepend>
        <q-icon name="event" />
      </template>
    </q-input>
    -->
    <!-- base_salary -->
    <q-input v-model="mega.base_salary.val" label="Оклад" type="number" mask="#"
      :options="{currency: false, autoDecimalMode: false}">
      <template v-slot:prepend>
        <q-icon name="attach_money" />
      </template>
    </q-input>

    <!-- bonus -->
    <q-input v-model="mega.bonus.val" label="Максимальная премия" type="number" mask="#"
      :options="{currency: false, autoDecimalMode: false}">
      <template v-slot:prepend>
        <q-icon name="emoji_events" />
      </template>
    </q-input>


    <!-- button -->
    <div class="row full-width no-wrap q-my-md">
      <q-btn color="red" icon="cancel" class="full-width" @click="SOT_clear()" v-if="mega.showOkbutton"></q-btn>
      <q-btn color="primary" icon="done" class="full-width" @click="SOT_dialog()" v-if="mega.showOkbutton" :disable = "mega.allowSOTsave"></q-btn>
    </div>

  </div>

  <q-separator color="grey"></q-separator>

  <!-- ГРЕЙД -->
  <div class="q-gutter-b-xs">

    <div class="text-h6 q-py-md">Грейды</div>

    <!-- id_sot_o -->
    <q-input v-model="mega.id_sot_o.val" readonly disable @update:model-value="val => SOT_find()">
      <template v-slot:prepend>
        <q-icon name="123" />
      </template>
    </q-input>

  </div>


</div>

`,
  setup() {
    const $q = useQuasar();
    var mega = reactive({
      id_sot_o: model.id_sot_o,
      city_id: model.city_id,
      group_id: model.group_id,
      date_begin: model.date_begin,
      base_salary: model.base_salary,
      bonus: model.bonus,

      user_email: model.user_email,

      Filials: [],
      Groups: [],

      showOkbutton: true,
      showGreid: computed(() => { return mega.id_sot_o.val == "" ? false : true }),
      allowSOTsave: computed(() => { return ["city_id", "group_id", "date_begin", "base_salary", "bonus"].some(item => mega[item].val == "") ? true : false }),
    })

    function SOT_clear() {
      Object.keys(mega).forEach(key => {
        if (mega[key]) {
          if (mega[key].hasOwnProperty("val")) {
            if (mega[key].val) {
              mega[key].val = ''
            }
          }
        }
      })
    }

    function SOT_dialog() {
      $q.dialog({
        title: 'Подтверждение',
        message: 'Сохранить СОТ?',
        cancel: true,
        persistent: true
      }).onOk(() => {
        console.log('>>>> OK');
        SOT_save();
      }).onCancel(() => {
        console.log('>>>> Cancel')
      })
    }

    async function SOT_find() {
      let date_begin = mega.date_begin.val.replace(/^(\d{4}-\d{2})-\d{2}(.*)$/, '$1-01');
      let [city_id, group_id] = [mega.city_id.val, mega.group_id.val];
      if ([city_id, group_id, date_begin].some(val => val == '')) return;

      let url =
        'https://script.google.com/macros/s/AKfycbxFA7VM6Xl9YVNZFiX2kIIv5U8Ne69Z6VANsT5l1n8ZqPO-1hjfl-ODHIpCi-lEXe_Dpw/exec?mode=getview',
        body = {
          mode: 'SOT_find',
          city_id: city_id.value,
          filial_name: city_id.label,
          group_id: group_id.value,
          group_name: group_id.label,
          date_begin: date_begin
        };

      let response = await fetch(url, {
        method: 'POST',
        muteHttpExceptions: false,
        body: JSON.stringify(body),
        // mode: 'no-cors', // no-cors, *cors, same-origin, cors
        headers: {
          // 'Content-Type': 'application/json',
          // 'Content-Type': "application/json; charset=UTF-8",
          // 'Content-Type': "multipart/form-data",
          'Content-Type': 'application/x-www-form-urlencoded',
          // 'Accept': 'application/json'
        },
      }
      ).then(resp => resp.json());
      mega.id_sot_o.val = response.id_sot_o;
      mega.allowSOTsave.val = response.allowSOTsave;
      console.log(response);
      if (response.id_sot_o != "") {
        notify('СОТ будет перезаписан', 'warning')
      }
    }

    async function SOT_save() {
      let url =
        'https://script.google.com/macros/s/AKfycbxFA7VM6Xl9YVNZFiX2kIIv5U8Ne69Z6VANsT5l1n8ZqPO-1hjfl-ODHIpCi-lEXe_Dpw/exec?mode=getview',
        body = {
          mode: 'SOT_save',
          id_sot_o: mega.id_sot_o.val.value,
          city_id: mega.city_id.val.value,
          filial_name: mega.city_id.val.label,
          group_id: mega.group_id.val.value,
          group_name: mega.group_id.val.label,
          date_begin: mega.date_begin.val,
          user_email: mega.user_email
        };
      let response = await fetch(url, {
        method: 'POST',
        muteHttpExceptions: false,
        body: JSON.stringify(body),
        // mode: 'no-cors', // no-cors, *cors, same-origin, cors
        headers: {
          // 'Content-Type': 'application/json',
          // 'Content-Type': "application/json; charset=UTF-8",
          // 'Content-Type': "multipart/form-data",
          'Content-Type': 'application/x-www-form-urlencoded',
          // 'Accept': 'application/json'
        },
      }
      ).then(resp => resp.json());

      if (response.answer) {
        notify('СОТ успешно записан', 'positive');
        SOT_clear();
      }
    }

    function ttt() {

    }

    function filterFilials(val, update) {
      if (val === '') {
        update(() => {
          mega.Filials = view.Filials;
        })
        return;
      }
      update(() => {
        const needle = val.toLowerCase()
        mega.Filials = (view.Filials.filter(v => v.label.toLowerCase().indexOf(needle) > -1));
      })
    }

    function filterGroups(val, update) {
      if (val === '') {
        update(() => {
          mega.Groups = view.Groups;
        })
        return;
      }
      update(() => {
        const needle = val.toLowerCase()
        mega.Groups = (view.Groups.filter(v => v.label.toLowerCase().indexOf(needle) > -1));
      })
    }

    async function getView() {
      let url =
        'https://script.google.com/macros/s/AKfycbxFA7VM6Xl9YVNZFiX2kIIv5U8Ne69Z6VANsT5l1n8ZqPO-1hjfl-ODHIpCi-lEXe_Dpw/exec?mode=getview';
      let response = await fetch(url, {
        method: 'GET',
        muteHttpExceptions: false,
        // body: JSON.stringify({ mode: 'folder', address: "yes" }),
        // mode: 'no-cors', // no-cors, *cors, same-origin, cors
        headers: {
          // 'Content-Type': 'application/json',
          // 'Content-Type': "application/json; charset=UTF-8",
          // 'Content-Type': "multipart/form-data",
          'Content-Type': 'application/x-www-form-urlencoded',
          // 'Accept': 'application/json'
        },
      }
      ).then(resp => resp.json());
      view = response;
      // mega.Filials = response.Filials;
      // mega.Groups = response.Groups;

      /*
        Object.entries(response).forEach(ent => {
        let key = ent[0];
        console.log(key, ent[1])
        if (modelc.hasOwnProperty(key)) {
        if (modelc[key].hasOwnProperty('val')) {
        if (modelc[key].val == '') {
        modelc[key].val = (ent[1] === null ? '' :ent[1])
        }
        } else {
        if (modelc[key] == '') {
        modelc[key] = (ent[1] === null ? '' :ent[1])
        }
        }
        }
        })
      */
    }

    function notify(text, type) {
      $q.notify({
        message: text,
        icon: 'announcement',
        timeout: 2000,
        type: type

      })
    }

    onBeforeMount(() => {
      getView();
    })

    // onMounted(() => {
    //   getView();
    // })

    watch(mega.date_begin, (value) => {
      mega.date_begin.val = value.val.replace(/^(\d{4}-\d{2})-\d{2}(.*)$/, '$1-01');
    })

    return {
      mega,
      filterFilials,
      filterGroups,
      SOT_clear,
      SOT_find,
      SOT_save,
      SOT_dialog,
      getView,
      notify,
      ttt
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