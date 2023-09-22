<template>
  <div>
    <center><h1>Lista za kupovinu</h1></center>
    <input v-model="newNote" @keyup.enter="addNote" placeholder="Dodaj novu stavku" />
    <!-- Dodajte element za prikaz upozorenja -->
    <div v-if="showDuplicateWarning" class="warning">Stavka već postoji!</div>
    <ul>
      <li v-for="note in notes" :key="note.id">
        <span v-if="note.id !== editingId">{{ note.text }}</span>
        <input v-else v-model="editedNote" @keyup.enter="updateNote" @keyup.esc="cancelEdit" />
        <button @click="removeNote(note.id)">Ukloni</button>
        <button @click="startEdit(note.id)">Uredi</button>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  data() {
    return {
      newNote: '',
      notes: [],
      editingId: null,
      editedNote: '',
      showDuplicateWarning: false, // Dodajte promenljivu za prikaz upozorenja
    };
  },
  methods: {
    addNote() {
      if (this.newNote.trim() === '') return;
      
      // Proverite da li je stavka već unesena
      if (this.notes.some((note) => note.text === this.newNote)) {
        this.showDuplicateWarning = true;
      } else {
        this.notes.push({ text: this.newNote, id: Date.now() });
        this.newNote = '';
        this.showDuplicateWarning = false;
      }
    },
    removeNote(id) {
      this.notes = this.notes.filter((note) => note.id !== id);
    },
    startEdit(id) {
      this.editingId = id;
      this.editedNote = this.notes.find((note) => note.id === id).text;
    },
    updateNote() {
      const noteIndex = this.notes.findIndex((note) => note.id === this.editingId);
      if (noteIndex !== -1) {
        // Proverite da li se novo ime već nalazi u listi
        const isDuplicate = this.notes.some((note, index) => {
          return index !== noteIndex && note.text === this.editedNote;
        });

        if (!isDuplicate) {
          this.notes[noteIndex].text = this.editedNote;
          this.editingId = null;
          this.editedNote = '';
        } else {
          this.showDuplicateWarning = true;
        }
      }
    },
    cancelEdit() {
      this.editingId = null;
      this.editedNote = '';
    },
  },
};
</script>
