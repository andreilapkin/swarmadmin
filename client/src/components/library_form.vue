<template>
    <v-dialog
      v-model="dialog"
      persistent
      max-width="600px"
    >
      <template v-slot:activator="{ on, attrs }">
        <v-btn
          color="primary"
          dark
          v-bind="attrs"
          v-on="on"
          class="mx-4"
        >
          Create new library
        </v-btn>
      </template>
      <v-card>
        <v-card-title>
          <span class="text-h5">Librariy File</span>
        </v-card-title>
        <v-card-text>
          <v-container>
            <v-row>
              <v-col cols="12">
                <v-text-field
                  label="Name*"
                  v-model="name"
                  required
                ></v-text-field>
              </v-col>

              <v-col cols="12">
                <v-tabs v-model="tab">
                  <v-tab
                    v-for="item in items"
                    :key="item"
                  >
                    {{ item }}
                  </v-tab>
                </v-tabs>
                <v-tabs-items v-model="tab">
                  <v-tab-item>
                    <v-card flat>
                      <v-textarea
                        filled
                        name="input-7-4"
                        v-model="file_content"
                        rows="20"
                      ></v-textarea>
                    </v-card>
                  </v-tab-item>
                  <v-tab-item>
                    <v-card flat>
                      <v-text-field
                        label="URL"
                        v-model="file_url"
                      ></v-text-field>
                    </v-card>
                  </v-tab-item>
                  <v-tab-item>
                    <v-card flat>
                      <v-file-input
                        truncate-length="15"
                        accept=".py,.txt"
                        show-size
                        v-model="file_upload"
                      ></v-file-input>
                    </v-card>
                  </v-tab-item>
                </v-tabs-items>

              </v-col>
            </v-row>
          </v-container>
          <small>*indicates required field</small>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn
            color="blue darken-1"
            text
            @click="dialog = false"
          >
            Close
          </v-btn>
          <v-btn
            color="blue darken-1"
            text
            @click="saveForm()"
          >
            Save
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
</template>


<script>
import axios from 'axios';
export default {
    data: () => ({
      sheet: false,
      dialog: false,
      name: '',
      file_content: '',
      file_url: '',
      file_upload: {},
      tab: null,
      items: [
        'Editor', 'URL', 'File Upload'
      ],
    }),
    methods: {
      submitData(name, content) {
        axios.post(`/api/lib/${name}`, {
          name: name,
          content: content
        })
        .then(response => {
          this.dialog = false;
          this.name = '';
          this.file_content = '';
          this.file_url = '';
          this.file_upload = {};
          this.tab = null;
          console.log(response);
          this.$parent.getLibfileList();
        })
        .catch(error => {
          console.log(error);
        });
      },
      saveForm () {
        switch (this.tab) {
          case 0:
            this.submitData(this.name, this.file_content);
            break;
          case 1:
            axios.get(this.file_upload).then(response => {
              this.submitData(this.name, response.data);
            });
            break;
          case 2:
            this.file_upload.text().then(content => {
              this.submitData(this.name, content);
            });
            break;
        }
      },
    },
}
</script>