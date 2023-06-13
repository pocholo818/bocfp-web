<template>
  <ion-page>
    <ion-split-pane content-id="main-content">
      <ion-menu content-id="main-content" type="overlay">
        <ion-content>

          <!-- image -->
          <img src="@/assets/images/logo.png" alt="Barangay Old Cabalan Logo"
            style="width: 45%; height: auto; margin-left: auto; margin-right: auto; display: block; margin-bottom: 24px">

          <ion-list id="inbox-list">
            <ion-list-header>Barangay Old Cabalan</ion-list-header>
            <!-- <ion-note>bofcp@gmail.com</ion-note> -->
            <ion-note>Hello {{ user_fname }}!</ion-note>

            <hr style="background-color: rgb(0, 0, 0, 12%); margin-bottom: 12px;">

            <template v-if="isLoaded">
              <ion-menu-toggle :auto-hide="false" v-for="(pages, i) in appPages" :key="i">
                <router-link :to="pages.url" activeClass="selected">
                  <ion-item lines="none" detail="false" class="hydrated" :class="{ 'selected': $route.name === pages.url }">
                    <ion-icon slot="start" :icon="pages.icon"></ion-icon>
                    <ion-label>{{ pages.title }}</ion-label>
                  </ion-item>
                </router-link>
              </ion-menu-toggle>
            </template>
          </ion-list>

          <ion-button id="button-logout" color="danger" @click="logout()" router-link="/login">
            <ion-icon :icon="logOutOutline"></ion-icon>&nbsp;Logout
          </ion-button>
        </ion-content>
      </ion-menu>
      <ion-router-outlet id="main-content"></ion-router-outlet>
    </ion-split-pane>
  </ion-page>
</template>

<script lang="ts">
// ionic stuff
import {
  IonContent,
  IonIcon,
  IonItem,
  IonLabel,
  IonList,
  IonListHeader,
  IonMenu,
  IonMenuToggle,
  IonNote,
  IonRouterOutlet,
  IonSplitPane,
  onIonViewDidEnter,
  toastController
} from '@ionic/vue';
// icons
import {
  logOutOutline,
  eyeOutline,
  grid,
  alertCircle,
  person,
  peopleCircle,
  accessibility
} from 'ionicons/icons';
import { defineComponent, ref } from 'vue';
import { useRoute } from 'vue-router';
import router from '@/router';

export default defineComponent({
  name: 'HomePage',
  components: {
    IonList,
    IonListHeader,
    IonMenu,
    IonMenuToggle,
    IonNote,
    IonRouterOutlet,
    IonSplitPane,
    IonIcon
  },
  ionViewWillEnter() {
    this.user_fname = localStorage.getItem('fname') || ''
    this.admin_power = localStorage.getItem('admin_power') || ''
    this.user_id = localStorage.getItem('user_id') || ''

    const appPages = [
      {
        title: 'Dashboard',
        url: '/dashboard',
        icon: grid
      },
      {
        title: 'Announcement',
        url: '/announcement',
        icon: alertCircle
      },
      {
        title: 'Children',
        url: '/child',
        icon: accessibility
      },
      {
        title: 'Guardian',
        url: '/guardian',
        icon: peopleCircle
      },
    ]

    // admin
    if (this.user_id === '1') {
      this.appPages = [
        ...appPages, // copy object content
        {
          title: 'Barangay Staff',
          url: '/user',
          icon: person
        }
      ]
    }
    // staff
    else {
      this.appPages = appPages
    }

    this.isLoaded = true
  },
  setup() {
    // sidebar pages
    const route = useRoute();

    return {
      logOutOutline,
      eyeOutline,
      isSelected: (url: string) => url === route.path ? 'selected' : ''
    }
  },
  data() {
    return {
      isLoaded: false,
      appPages: [{}],
      user_fname: '',
      admin_power: '',
      user_id: ""
    }
  },
  methods: {
    async logout() {
      const toast = await toastController.create({
        duration: 1500,
        position: 'top'
      })

      toast.message = 'Logout Success!'
      localStorage.removeItem('user_id')
      localStorage.removeItem('fname')
      localStorage.removeItem('lname')
      localStorage.removeItem('admin_power')
      localStorage.removeItem('access_token')
      localStorage.removeItem('refresh_token')
      localStorage.setItem('is_logged_in', 'false')
      
      await toast.present();
    }
  }
});
</script>

<style scoped>
a {
  text-decoration: none;
}

ion-menu {
  border-right: none;
}

ion-menu,
ion-menu ion-content {
  --background: var(--ion-color-primary);
  --color: white;
}

ion-menu::part(container) {
  /* max-width: 100px; */
  /* max-width: 100px;
  min-width: 100px; */
}

ion-menu.md ion-content {
  /* --padding-start: 16px; */
  /* --padding-end: 16px; */
  --padding-top: 24px;
  --padding-bottom: 16px;
  border-right: 1px solid var(--ion-color-primary-shade);
}

ion-menu ion-content::part(scroll) {
  display: flex;
  flex-direction: column;
}

ion-split-pane {
  --side-max-width: 22%;
  --side-min-width: 300px;
}

ion-menu.md ion-note {
  margin-bottom: 12px;
}

ion-menu.md ion-list-header,
ion-menu.md ion-note {
  padding-left: 20px;
  padding-right: 20px;
}

ion-menu.md ion-list#inbox-list {
  /* border-bottom: 1px solid var(--ion-color-step-150, #d7d8da);  */
  background: var(--ion-color-primary-shade);
  --color: white;
  padding: 20px 0 4px;
  margin: calc(12px + 1%);
  border-radius: 6px;
}

ion-menu.md ion-list#inbox-list ion-list-header {
  font-size: 24px;
  font-weight: 700;
  color: white;
  min-height: 40px;
  margin-bottom: 4px;
}

ion-note {
  display: inline-block;
  font-size: 16px;

  color: white;
}



/* nav item */
ion-menu.md ion-item {
  --padding-start: 16px;
  /* --padding-end: 24px; */
  --border-radius: 4px;
}

ion-menu.md ion-item ion-icon,
ion-menu.md ion-item ion-label {
  color: white;
}

ion-menu.md a ion-item {
  --background: transparent;
  padding: 0 12px 8px;
}

ion-menu.md a.selected ion-item {
  --background: white;
}

ion-menu.md a.selected ion-item::part(native) {
  box-shadow: 1px 1px 8px 0 rgb(0, 0, 0, 16%);
}

ion-menu.md a.selected ion-item ion-icon {
  color: var(--ion-color-primary);
}

ion-menu.md ion-item ion-label {
  font-weight: 500;
}

ion-menu.md a.selected ion-item ion-label {
  color: var(--ion-color-primary);
  font-weight: 600;
  /* text-decoration: underline; */
}

/* ion-item {
  padding-bottom: 12px;
} */

#button-logout {
  margin: auto 0 0;
  padding: 0 calc(12px + 1%);
}
</style>