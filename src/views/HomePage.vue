<template>
  <ion-page>
    <ion-header>
      <ion-toolbar>
        <ion-title>Note App</ion-title>
      </ion-toolbar>
    </ion-header>

    <ion-content>
      <div class="container">
        <ion-grid>
          <ion-row>
            <ion-col size="12" size-md="6" offset-md="3">
              <ion-list>
                <ion-list-header>Notes</ion-list-header>
                <ion-item v-for="note in notes" :key="note.id">
                  <ion-note :style="{ backgroundColor: note.color }">
                    <h2>{{ note.title }}</h2>
                    <p>{{ note.content }}</p>
                    <div v-if="note.type === 'image'">
                      <img
                        :src="note.file"
                        alt="Uploaded Image"
                        style="max-width: 100%; max-height: 200px;"
                      />
                    </div>
                    <div v-else-if="note.type === 'video'">
                      <video
                        controls
                        :src="note.file"
                        style="max-width: 100%; max-height: 200px;"
                      ></video>
                    </div>
                    <div v-else-if="note.type === 'audio'">
                      <audio controls :src="note.file"></audio>
                    </div>
                  </ion-note>
                  <!-- Buttons for each note -->
                  <ion-button color="warning" @click="editNote(note)">
                    <ion-icon name="create"></ion-icon>
                  </ion-button>
                  <ion-button color="danger" @click="deleteNote(note)">
                    <ion-icon name="trash"></ion-icon>
                  </ion-button>
                </ion-item>
              </ion-list>
            </ion-col>
          </ion-row>
        </ion-grid>
      </div>
      <ion-fab vertical="bottom" horizontal="start" slot="fixed">
        <ion-fab-button @click="openModal('add')">
          <ion-icon name="add">+</ion-icon>
        </ion-fab-button>
      </ion-fab>
    </ion-content>

    <!-- Modal -->
    <ion-modal :is-open="isModalOpen">
      <ion-content>
        <ion-list>
          <ion-item>
            <ion-label position="stacked">Title</ion-label>
            <ion-input v-model="modalData.title"></ion-input>
          </ion-item>
          <ion-item>
            <ion-label position="stacked">Content</ion-label>
            <ion-textarea v-model="modalData.content"></ion-textarea>
          </ion-item>
          <ion-item>
            <ion-label position="stacked">File</ion-label>
            <input type="file" @change="handleFileUpload" />
          </ion-item>
          <ion-item v-if="modalData.file">
            <div v-if="modalData.type === 'image'">
              <img
                :src="modalData.file"
                alt="Uploaded Image"
                style="max-width: 100%; max-height: 200px;"
              />
            </div>
            <div v-else-if="modalData.type === 'video'">
              <video
                controls
                :src="modalData.file"
                style="max-width: 100%; max-height: 200px;"
              ></video>
            </div>
            <div v-else-if="modalData.type === 'audio'">
              <audio controls :src="modalData.file"></audio>
            </div>
          </ion-item>
        </ion-list>
        <ion-button expand="block" @click="saveNote">Save</ion-button>
        <ion-button expand="block" @click="closeModal">Cancel</ion-button>
      </ion-content>
    </ion-modal>
    <!-- End Modal -->
  </ion-page>
</template>

<script>
import {
  IonPage,
  IonHeader,
  IonToolbar,
  IonTitle,
  IonContent,
  IonGrid,
  IonRow,
  IonCol,
  IonList,
  IonListHeader,
  IonItem,
  IonNote,
  IonFab,
  IonFabButton,
  IonIcon,
  IonModal,
  IonButton,
  IonInput,
  IonTextarea,
} from "@ionic/vue";

export default {
  name: "NoteApp",
  components: {
    IonPage,
    IonHeader,
    IonToolbar,
    IonTitle,
    IonContent,
    IonGrid,
    IonRow,
    IonCol,
    IonList,
    IonListHeader,
    IonItem,
    IonNote,
    IonFab,
    IonFabButton,
    IonIcon,
    IonModal,
    IonButton,
    IonInput,
    IonTextarea,
  },
  data() {
    return {
      notes: [
        {
          id: 1,
          title: "Project Meeting Agenda",
          content:
            "Discuss project timeline, resource allocation, and potential risks. Include action items for each team member.",
          color: "#ffd6a5", // Light orange for priority
          file: null, // No file attached
          type: null, // Default note type
        },
        {
          id: 2,
          title: "Birthday Party Reminder",
          content:
            "Buy cake, send invitations, and order decorations. Party is on April 15th at 7 PM.",
          color: "#caffbf", // Light green for events
          file: null, // No file attached
          type: null, // Default note type
        },
        {
          id: 3,
          title: "Project Tasks",
          content:
            "1. Design UI\n2. Implement Backend\n3. Test Functionality\n4. Deploy to Production",
          color: "#9bf6ff", // Light blue for projects
          file: null, // No file attached
          type: null, // Default note type
        },
      ],
      isModalOpen: false,
      modalData: {
        title: "",
        content: "",
        color: "",
        file: null,
        type: null,
      },
    };
  },
  created() {
    const savedNotes = localStorage.getItem("notes");
    if (savedNotes) {
      this.notes = JSON.parse(savedNotes);
    }
  },
  methods: {
    openModal(mode) {
      if (mode === "add") {
        this.modalData = {
          title: "",
          content: "",
          color: this.getRandomColor(),
          file: null,
          type: null,
        };
      } else {
        // Implement logic for editing a note
      }
      this.isModalOpen = true;
    },
    closeModal() {
      this.isModalOpen = false;
    },
    saveNote() {
      const newNote = {
        id: this.notes.length + 1,
        title: this.modalData.title,
        content: this.modalData.content,
        color: this.getRandomColor(),
        file: this.modalData.file,
        type: this.modalData.type,
      };
      this.notes.push(newNote);
      this.isModalOpen = false;

      localStorage.setItem("notes", JSON.stringify(this.notes));
    },
    handleFileUpload(event) {
      const file = event.target.files[0];
      if (file) {
        const reader = new FileReader();
        reader.onload = (e) => {
          const fileType = file.type.split("/")[0]; // Get the type of file (image, video, audio)
          this.modalData.file = e.target.result;
          this.modalData.type = fileType;
        };
        reader.readAsDataURL(file);
      }
    },
    getRandomColor() {
      const letters = "0123456789ABCDEF";
      let color = "#";
      for (let i = 0; i < 6; i++) {
        color += letters[Math.floor(Math.random() * 16)];
      }
      return color;
    },
    editNote(note) {
      // Set modal data with the note to be edited
      this.modalData = {
        id: note.id,
        title: note.title,
        content: note.content,
        color: note.color,
        file: note.file,
        type: note.type,
      };
      // Open the modal
      this.isModalOpen = true;
    },
    deleteNote(note) {
      // Implement the logic to handle deletion here
      const index = this.notes.findIndex((item) => item.id === note.id);
      if (index !== -1) {
        this.notes.splice(index, 1); // Remove the note from the array
        // Optionally, you can also update local storage here if needed
        localStorage.setItem("notes", JSON.stringify(this.notes));
      }
    },
  },
};
</script>

<style>
ion-note {
  width: 100%;
  padding: 20px;
  border-radius: 5px;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
}

ion-note h2 {
  margin-top: 0;
  color: #333;
}

ion-note p {
  color: #555;
}
</style>
