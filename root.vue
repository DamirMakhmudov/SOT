const { createApp, ref, reactive, computed, watch, onMounted, watchEffect, onBeforeUnmount } = Vue;
const { useQuasar, Loading, QSpinnerGears } = Quasar;

var vueObject = {
  name: "root",
  template:
    /*html*/
    `

<div class="q-pa-md fit column">

  <div class="q-gutter-xs"> <!-- style="height:220px" -->

    <div class="text-h5">Создание СОТ Операторов</div>

    <!-- id_sot_o -->
    <q-input v-model="mega.id_sot_o.val" readonly disable @update:model-value="val => fff(val)">
      <template v-slot:prepend>
        <q-icon name="123" />
      </template>
    </q-input>

    <!-- city_id -->
    <q-select v-model="mega.city_id.val" use-input input-debounce="0" label="Филиал" :options="mega.Filials" behavior="menu" @filter="filterFilials">
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
    <q-select v-model="mega.group_id.val" use-input input-debounce="0" label="Группа" :options="mega.Groups" behavior="menu" @filter="filterGroups" @update:model-value="val => fff(val)">
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
    <q-input v-model="mega.date_begin.val" type="date">
      <!-- hint="Native date" -->
      <template v-slot:prepend>
        <q-icon name="event" />
      </template>
    </q-input>

    <!-- base_salary -->
    <q-input v-model="mega.base_salary.val" label="Оклад" type="number" mask="#"
      :options="{currency: false, autoDecimalMode: false}">
      <template v-slot:prepend>
        <q-icon name="attach_money" />
      </template>
    </q-input>

    <!-- bonus -->
    <q-input v-model="mega.bonus.val" label="Максимальная премия" type="number" mask="#" :options="{currency: false, autoDecimalMode: false}" >
      <template v-slot:prepend>
        <q-icon name="emoji_events" />
      </template>
    </q-input>

    <!-- button -->
    <div class="row full-width no-wrap q-my-md">
      <q-btn color="red" icon="cancel" class="full-width" @click="clearData()" v-if="mega.showOkbutton"></q-btn>
      <q-btn color="primary" icon="done" class="full-width" @click="getView()" v-if="mega.showOkbutton"></q-btn>
    </div>

  </div>

  <q-separator color="grey"></q-separator>

  <!-- ГРЕЙД -->
  <div class="q-gutter-b-xs">

    <div class="text-h5 q-py-md">Грейды</div>

    <!-- id_sot_o -->
    <q-input v-model="mega.id_sot_o.val" readonly disable @update:model-value="val => fff(val)">
      <template v-slot:prepend>
        <q-icon name="123" />
      </template>
    </q-input>

  </div>


</div>

`,
  setup() {
    var mega = reactive({
      id_sot_o: model.id_sot_o,
      city_id: model.city_id,
      group_id: model.group_id,
      date_begin: model.date_begin,
      base_salary: model.base_salary,
      bonus: model.bonus,

      Filials: view.Filials,
      Groups: view.Groups,

      showOkbutton: true
    })

    function clearData() {
      Object.keys(mega).forEach(key => {
        if (mega[key].val) {
          mega[key].val = ''
        }
      })
    }

    function fff(val) {

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
      let url = 'https://script.google.com/macros/s/AKfycbxFA7VM6Xl9YVNZFiX2kIIv5U8Ne69Z6VANsT5l1n8ZqPO-1hjfl-ODHIpCi-lEXe_Dpw/exec?mode=getview';
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

      // Object.entries(response).forEach(ent => {
      //   let key = ent[0];
      //   console.log(key, ent[1])
      //   if (modelc.hasOwnProperty(key)) {
      //     if (modelc[key].hasOwnProperty('val')) {
      //       if (modelc[key].val == '') {
      //         modelc[key].val = (ent[1] === null ? '' :ent[1])
      //       }
      //     } else {
      //       if (modelc[key] == '') {
      //         modelc[key] = (ent[1] === null ? '' :ent[1])
      //       }
      //     }
      //   }
      // })
    }

    onMounted(() => {
      getView()
    })

    return {
      mega,
      filterFilials,
      filterGroups,
      clearData,
      fff,
      getView
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