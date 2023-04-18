const { createApp, ref, reactive, computed, watch, onMounted, watchEffect, onBeforeUnmount } = Vue;
const { useQuasar, Loading, QSpinnerGears } = Quasar;

var vueObject = {
name: "root",
template:
/*html*/
`
<div class="q-pa-md fit column">

  <div class="q-gutter-x-xs"> <!-- style="height:220px" -->

    <div class="text-h5">Создание СОТ Операторов</div>

    <!-- id_sot_o -->
    <q-input v-model="mega.id_sot_o.val" readonly disable @update:model-value="val => fff(val)">
      <template v-slot:prepend>
        <q-icon name="123" />
      </template>
    </q-input>

    <!-- city_id -->
    <div class="text q-mb-xs">{{mega.city_id.val}}</div>
    <q-select v-model="mega.city_id.val" use-input input-debounce="0" label="Филиал" :options="mega.Filials"
      behavior="menu" @filter="filterFilials">
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
    <div class="text q-mb-xs">{{mega.group_id.val}}</div>
    <q-select v-model="mega.group_id.val" use-input input-debounce="0" label="Группа" :options="mega.Groups"
      behavior="menu" @filter="filterGroups" @update:model-value="val => fff(val)">
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
    <div class="text q-mb-xs">{{mega.date_begin.val}}</div>
    <q-input v-model="mega.date_begin.val" type="date" @update:model-value="val => fff(val)">
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
    <div class="row full-width no-wrap q-mb-md">
      <q-btn color="red" icon="cancel" class="full-width" @click="saveData()" v-if="mega.showOkbutton"></q-btn>
      <q-btn color="primary" icon="done" class="full-width" @click="saveData()" v-if="mega.showOkbutton"></q-btn>
    </div>

  </div>

  <q-separator color="grey"></q-separator>

  <div class="q-gutter-x-xs">

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
Filials: model.Filials,
Groups: model.Groups,

showOkbutton: true
})

function fff(val) {
// console.log(val)
}

function filterFilials(val, update) {
if (val === '') {
update(() => {
mega.Filials = model.Filials;
})
return;
}
update(() => {
const needle = val.toLowerCase()
mega.Filials = (model.Filials.filter(v => v.label.toLowerCase().indexOf(needle) > -1));
})
}

function filterGroups(val, update) {
if (val === '') {
update(() => {
mega.Filials = model.Groups;
})
return;
}
update(() => {
const needle = val.toLowerCase()
mega.Groups = (model.Groups.filter(v => v.label.toLowerCase().indexOf(needle) > -1));
})
}

return {
mega,
filterFilials,
filterGroups,
fff
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