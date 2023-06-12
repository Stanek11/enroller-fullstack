<template>
  <div>
    <NewMeetingForm @added="addNewMeeting($event)"></NewMeetingForm>
    <span v-if="meetings.length === 0">Brak zaplanowanych spotkań.</span>
    <h3 v-else>Zaplanowane zajęcia ({{ meetings.length }})</h3>
    <MeetingsList :meetings="meetings"
                  :username="username"
                  @attend="addMeetingParticipant($event)"
                  @unattend="removeMeetingParticipant($event)"
                  @delete="deleteMeeting($event)"></MeetingsList>
  </div>
</template>

<script>
import NewMeetingForm from "./NewMeetingForm";
import MeetingsList from "./MeetingsList";
import axios from "axios";

export default {
  components: {NewMeetingForm, MeetingsList},
  props: {username: String},
  data() {
    return {
      meetings: [],
      message: '',
      meetingCreated: false,
    };
  },

  methods: {
    addNewMeeting(meeting) {
      axios.post('/api/meetings', meeting)
                .then(response => {
                  this.message = ("Udało się dodać spotkanie")
                  this.meetings.push({
                    id: response.data.id,
                    ...meeting
                  })
                })
                .catch(response => {
                  this.message = ("Nie udało się dodać spotkania")
                });
    },
    addMeetingParticipant(meeting) {
      meeting.participants.push(this.username);
    },
    removeMeetingParticipant(meeting) {
      meeting.participants.splice(meeting.participants.indexOf(this.username), 1);
    },
    deleteMeeting(meeting) {
      axios.delete('/api/meetings/' + meeting.id)
                .then(response => {
                  this.message = ("Udało się usunąć spotkanie")
                  this.meetings.splice(this.meetings.indexOf(meeting), 1);
                })
                .catch(response => {
                  this.message = ("Nie udało się dodać spotkania")
                });
    },
  }
}
</script>

<style>
.alert {
    padding: 5px;
    border: 2px solid green;
    background: lightgreen;
    font-size: 2em;
    color: darkgreen;
    text-align: center;
}

.alert-red {
    padding: 5px;
    font-size: 2em;
    color: darkred;
    border: 2px solid red;
    background: lightcoral;
    text-align: center;
}
</style>