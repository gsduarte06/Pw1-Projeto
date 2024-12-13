<template>
  <div class="d-flex flex-column" style="background-color: #00041f">
    <div class="d-flex flex-row justify-space-evenly mt-16">
      <v-container fluid style="padding: 20px">
        <v-row justify="center">
          <v-col cols="12" md="8">
            <v-card class="mx-auto elevation-0" style="border-radius: 12px; background-color: #00041f; margin-top: 15px;">
              <v-card-text>
                <v-row align="start">
                  <!-- Avatar and Divider -->
                  <v-col cols="12" md="4" class="d-flex justify-center align-center" style="margin-top: 90px;">
                    <v-avatar size="200" class="profile-avatar">
                      <img src="../assets/images/profile_image.jpg" alt="User's Profile Picture" />
                    </v-avatar>
                  </v-col>

                  <!-- Adjusted Divider -->
                  <v-col cols="1" class="divider d-flex justify-center align-stretch"></v-col>

                  <v-col cols="12" md="7" style="margin-top: 20px; padding-left: 10px; padding-right: 10px;">
                    <!-- User Info (Name, Email, etc.) -->
                    <v-row align="center" style="margin-bottom: 8px;">
                      <v-col cols="4">
                        <label class="text-white custom-label">Name:</label>
                      </v-col>
                      <v-col cols="8">
                        <span class="text-white font-weight-regular" style="text-align: left; display: block;">
                          {{ user.name }}
                        </span>
                      </v-col>
                    </v-row>
                    <v-row align="center" style="margin-bottom: 8px;">
                      <v-col cols="4">
                        <label class="text-white custom-label">Email:</label>
                      </v-col>
                      <v-col cols="8">
                        <span class="text-white font-weight-regular" style="text-align: left; display: block;">
                          {{ user.email }}
                        </span>
                      </v-col>
                    </v-row>
                    <v-row align="center" style="margin-bottom: 8px;">
                      <v-col cols="4">
                        <label class="text-white custom-label">Password:</label>
                      </v-col>
                      <v-col cols="8">
                        <span class="text-white font-weight-regular" style="text-align: left; display: block;">
                          {{ user.password.replace(/./g, '●') }}
                        </span>
                      </v-col>
                    </v-row>
                    <v-row align="start" style="margin-bottom: 8px;">
                      <v-col cols="4">
                        <label class="text-white custom-label">Bio:</label>
                      </v-col>
                      <v-col cols="8">
                        <span class="text-white font-weight-regular"
                          style="font-size: 18px; text-align: left; display: block;">
                          {{ user.bio }}
                        </span>
                      </v-col>
                    </v-row>

                    <!-- Points info placed below Bio -->
                    <v-row align="center" style="margin-bottom: 8px;">
                      <v-col cols="4">
                        <label class="text-white custom-label">Points:</label>
                      </v-col>
                      <v-col cols="8">
                        <span class="text-white font-weight-regular" style="text-align: left; display: block;">
                          {{ user.points }}
                        </span>
                      </v-col>
                    </v-row>

                    <!-- Icon Badges Section -->
                    <v-row align="start" style="margin-bottom: 8px;">
                      <v-col cols="4">
                        <label class="text-white custom-label">Badges:</label>
                      </v-col>
                      <v-col cols="8">
                        <div class="d-flex flex-wrap">
                          <v-tooltip
                            v-for="badge in user.badges"
                            :key="badge.id"
                            top
                            :open-on-hover="true"
                            transition="scale-transition"
                          >
                            <template v-slot:activator="{ props }">
                              <v-chip
                                class="badge-chip"
                                v-bind="props"
                                :style="badge.achieved 
                                  ? 'background-color: #ff00ee; color: white;' 
                                  : 'background-color: #555; color: #aaa;'"
                                style="margin: 4px;"
                                @click="showBadgeDetails(badge)"
                              >
                                <v-icon>{{ badge.icon }}</v-icon>
                              </v-chip>
                            </template>
                            <span>{{ badge.title }}</span>
                          </v-tooltip>
                        </div>
                      </v-col>
                    </v-row>

                    <!-- Modal for Badge Details -->
                    <v-dialog v-model="badgeModal" max-width="500px">
                      <v-card class="badge-modal-card">
                        <v-card-title class="badge-modal-title">
                          {{ selectedBadge?.title || 'Badge Details' }}
                        </v-card-title>
                        <v-card-text>
                          <p v-if="selectedBadge?.achieved" class="text-white text-body1">
                            {{ selectedBadge.description }}
                          </p>
                          <p v-else class="text-white text-body1">
                            You haven’t obtained this badge yet!
                          </p>
                        </v-card-text>
                        <v-card-actions>
                          <v-btn text @click="badgeModal = false" class="btn-close">
                            Close
                          </v-btn>
                        </v-card-actions>
                      </v-card>
                    </v-dialog>

                  </v-col>
                </v-row>
              </v-card-text>
            </v-card>

            <!-- Edit Button -->
            <v-row justify="center" class="mt-4">
              <v-btn class="edit-btn" @click="editDialog = true">
                Edit Data
              </v-btn>
            </v-row>
            <v-dialog v-model="editDialog" max-width="600px">
              <v-card class="edit-dialog-card" style="padding-top: 16px; padding-bottom: 16px;">
                <v-card-title class="edit-dialog-title">
                  Edit User Details
                </v-card-title>
                <v-card-text style="padding-top: 8px; padding-bottom: 8px;">
                  <v-form ref="editForm">
                    <v-row align="center" style="margin-bottom: 8px;">
                      <v-col cols="4">
                        <label class="text-white text-body1 font-weight-medium">Name:</label>
                      </v-col>
                      <v-col cols="8">
                        <v-text-field class="text-white" v-model="editedUser.name" variant="underlined"
                          density="comfortable"></v-text-field>
                      </v-col>
                    </v-row>
                    <v-row align="center" style="margin-bottom: 8px;">
                      <v-col cols="4">
                        <label class="text-white text-body1 font-weight-medium">Email:</label>
                      </v-col>
                      <v-col cols="8">
                        <v-text-field class="text-white" v-model="editedUser.email" variant="underlined"
                          density="comfortable"></v-text-field>
                      </v-col>
                    </v-row>
                    <v-row align="center" style="margin-bottom: 8px;">
                      <v-col cols="4">
                        <label class="text-white text-body1 font-weight-medium">Password:</label>
                      </v-col>
                      <v-col cols="8">
                        <v-text-field class="text-white" v-model="editedUser.password" type="password"
                          variant="underlined" density="comfortable"></v-text-field>
                      </v-col>
                    </v-row>
                    <v-row align="start" style="margin-bottom: 8px; " class="">
                      <v-col cols="4">
                        <label class="text-white text-body1 font-weight-medium">Bio:</label>
                      </v-col>
                      <v-col cols="8">
                        <v-textarea class="text-white" v-model="editedUser.bio" variant="underlined"
                          density="comfortable" no-resize auto-grow rows="2"></v-textarea>
                      </v-col>
                    </v-row>
                  </v-form>
                </v-card-text>
                <v-card-actions style="padding-top: 8px;" class="mr-4">
                  <v-btn text @click="editDialog = false" class="btn-cancel">
                    Cancel
                  </v-btn>
                  <v-btn text @click="saveData" class="btn-save">
                    Save
                  </v-btn>
                </v-card-actions>
              </v-card>
            </v-dialog>

            <div class="d-flex flex-column align-self-center mt-16">
              <p class="text-h3 " style="color:#ff00ee">Your Events</p>
              <div class="d-flex flex-column">
                <!-- Cards -->
                <div v-for="event in events" :key="event.id" class="d-flex flex-row mt-16">
                  <div class=" d-flex flex-column align-center justify-center text-white text-body1 font-weight-medium"
                    style="background-color: #000B52; min-height: 180px; width:10%">
                    <p> {{ event.BeginDateSmall.split(" ")[0] }}</p>
                    <p>{{ event.BeginDateSmall.split(" ")[1] }}</p>
                  </div>
                  <div class="d-flex flex-column justify-space-between ma-2 ml-5 " style="width:90%">
                    <p class="text-white text-h4">{{ event.Title }}</p>
                    <p class="text-white text-body1">{{ event.location }}</p>
                    <p class="text-white text-body1">{{ event.description }}</p>
                  </div>
                </div>
              </div>
            </div>
          </v-col>
        </v-row>
      </v-container>
    </div>
  </div>
</template>


<script>
export default {
  data() {
    return {
      user: {
        name: "Jessica Fletcher",
        email: "jessica@example.com",
        password: "mysecurepassword",
        bio: "Fusce nisi leo, porta nec diam vitae, badictum fermentum odio. Fusce venenatis, tortor in imperdiet semper, ante arcu accumsan orci.",
        points: 1200,
        badges: [
        { id: 1, icon: 'mdi-cart', title: 'Biggest Buyer', description: 'Awarded for purchasing the most items in a single month.', achieved: true },
        { id: 2, icon: 'mdi-account-group', title: 'Best Attendance', description: 'Recognized for attending every event in a year.', achieved: true },
        { id: 3, icon: 'mdi-calendar-star', title: 'Event Star', description: 'Earned for organizing three successful events.', achieved: false },
        { id: 4, icon: 'mdi-code-tags', title: 'Tech Enthusiast', description: 'For contributing code to open-source projects.', achieved: false },
        { id: 5, icon: 'mdi-earth', title: 'Global Networker', description: 'Granted for connecting with members from five countries.', achieved: true },
        { id: 6, icon: 'mdi-star', title: 'Rising Star', description: 'Given to a participant who showed exceptional progress in the workshops or coding bootcamps.', achieved: true },
        { id: 7, icon: 'mdi-trophy', title: 'Innovator of the Year', description: 'Awarded to the participant who proposed the most groundbreaking idea during the event\'s innovation challenge.', achieved: false },
        { id: 8, icon: 'mdi-hand-heart', title: 'Event Ally', description: 'For assisting the most attendees during the event by sharing knowledge or providing guidance.', achieved: true },
        { id: 9, icon: 'mdi-code-braces', title: 'Hackathon Master', description: 'Recognized for leading a winning team in the event’s hackathon.', achieved: false },
        { id: 10, icon: 'mdi-microphone', title: 'Inspirational Speaker', description: 'Given to keynote speakers who delivered compelling and impactful sessions.', achieved: true },
        { id: 11, icon: 'mdi-leaf', title: 'Eco-Tech Advocate', description: 'Awarded for presenting sustainable tech solutions during the event.', achieved: false },
        { id: 12, icon: 'mdi-account-multiple', title: 'Most Connected', description: 'Granted to attendees who networked with 50+ participants during the event.', achieved: true },
        { id: 13, icon: 'mdi-rocket', title: 'Tech Trailblazer', description: 'For showcasing a product or prototype that adopts emerging technologies.', achieved: false },
        { id: 14, icon: 'mdi-shield-check', title: 'Cybersecurity Advocate', description: 'Given to participants who presented significant contributions to securing digital ecosystems.', achieved: true },
      ],

      },
      badgeModal: false,
      selectedBadge: null,
      editedUser: {},
      editDialog: false,
      events: [
        {
          id: 1,
          BeginDateSmall: "24 Mar",
          Title: "Vue.js Meetup",
          location: "San Francisco, CA",
          description: "Join us for a meetup to discuss the latest trends and best practices in Vue.js development.",
        },
        {
          id: 2,
          BeginDateSmall: "4 April",
          Title: "JavaScript Conference",
          location: "New York, NY",
          description: "A major conference for JavaScript developers. Workshops, keynotes, and networking opportunities.",
        },
        {
          id: 3,
          BeginDateSmall: "15 April",
          Title: "Web Development Bootcamp",
          location: "Los Angeles, CA",
          description: "A week-long bootcamp for aspiring web developers. Intensive training in HTML, CSS, and JavaScript.",
        },
      ],
    };
  },
  methods: {
    showBadgeDetails(badge) {
      this.selectedBadge = badge;
      this.badgeModal = true;
    },

    saveData() {
      Object.assign(this.user, this.editedUser);
      this.editDialog = false;
    },
  },
  watch: {
    editDialog(val) {
      if (val) {
        this.editedUser = { ...this.user };
      }
    },
  },
};
</script>

<style scoped>
.profile-avatar img {
  object-fit: cover;
  width: 100%;
  height: 100%;
}

.divider {
  border-left: 3px solid #ff00ee;
  height: 500px;
}

.v-btn {
  text-transform: none;
}

.edit-dialog-card {
  background: linear-gradient(135deg, #1a1a2e, #262640);
  border-radius: 16px;
  box-shadow: 0 4px 15px rgba(0, 0, 0, 0.5);
}

.edit-dialog-title {
  color: #ff00ee;
  font-weight: 600;
  font-size: 24px;
  text-align: center;
}

.input-field {
  background-color: #262640;
  color: white;
  border-radius: 12px;
}

.input-field .v-input__control {
  border-radius: 12px;
}

.input-field .v-input__slot {
  border-radius: 12px;
}

.edit-btn {
  background-color: #ff00ee;
  color: white;
  width: 200px;
  height: 50px;
  border-radius: 8px;
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.4);
}

.edit-btn:hover {
  background-color: #ff33cc;
}

.btn-cancel {
  color: #ff00ee;
  font-weight: bold;
}

.btn-save {
  color: #ffffff;
  background-color: #ff00ee;
  font-weight: bold;
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.4);
  border-radius: 8px;
}

.btn-save:hover {
  background-color: #ff33cc;
}

.date-rectangle {
  padding: 24px;
  height: 100px;
  text-align: center;
  display: flex;
  justify-content: center;
  align-items: center;
}

.event-details {
  padding-left: 24px;
}

.event-title {
  font-size: 20px;
  font-weight: 600;
}

.event-details p {
  margin-top: 8px;
  font-size: 16px;
}

.custom-label {
  font-weight: 600;
  font-size: 20px;
}

.badge-modal-card {
  background: linear-gradient(135deg, #1a1a2e, #262640);
  border-radius: 16px;
  box-shadow: 0 4px 15px rgba(0, 0, 0, 0.5);
  padding: 20px;
}

.badge-modal-title {
  color: #ff00ee;
  font-weight: bold;
  font-size: 20px;
}

.btn-close {
  color: #ff00ee;
  font-weight: bold;
}


@media (max-width: 600px) {
  .profile-avatar img {
    width: 150px;
    /* Adjust size for smaller screens */
    height: 150px;
  }

  .edit-btn {
    width: 180px;
    /* Adjust button width for smaller screens */
  }

  .event-row {
    flex-direction: column;
  }
  
}
</style>