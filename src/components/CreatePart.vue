<template>
  <div class="home">
    <div class="d-flex justify-center mb-6">
      <v-card class="elevation-12" min-width="800">
        <v-toolbar dark color="primary">
          <v-toolbar-title>Create Part</v-toolbar-title>
        </v-toolbar>
        <v-card-text>
          <form>
            <v-text-field
              v-model="partNumberInput"
              :error-messages="partNumberErrors"
              :counter="40"
              label="Part Number"
              required
              @input="$v.partNumberInput.$touch()"
              @blur="$v.partNumberInput.$touch()"
            ></v-text-field>
            <v-text-field
              v-model="partDescription"
              :error-messages="emailErrors"
              label="Part Description"
              required
              @input="$v.partDescription.$touch()"
              @blur="$v.partDescription.$touch()"
            ></v-text-field>
            <v-btn class="mr-4" @click="submit"> submit </v-btn>
            <v-btn @click="clear"> clear </v-btn>
          </form>
        </v-card-text>
      </v-card>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import { required, maxLength } from "vuelidate/lib/validators";

export default {
  name: "Part",
  props: {
    msg: String,
  },
  validations: {
    partNumberInput: { required, maxLength: maxLength(40) },
  },
  data() {
    return {
      parts: [],
      partNumber: "",
      partDescription: "",
      partPostResponse: "",
      search: "",
      partNumberInput: "",
    };
  },
  mounted() {
    axios
      .get("http://localhost:45456/partservice")
      .then((response) => (this.parts = response.data));
  },
  methods: {
    submit() {
      if (!this.$v.$error) {
        axios
          .post("http://localhost:45456/partservice", {
            partNumber: this.partNumberInput,
            partDescription: this.partDescription,
          })
          .then((response) => (this.partPostResponse = response.data));
      }
    },
  },
  computed: {
    headers() {
      return [
        { text: "Part Number", value: "partNumber" },
        { text: "Part Description", value: "partDescription" },
      ];
    },
    partNumberErrors() {
      const errors = [];
      if (!this.$v.partNumberInput.$dirty) return errors;
      !this.$v.partNumberInput.maxLength &&
        errors.push("Part Number must be at most 40 characters long");
      !this.$v.partNumberInput.required &&
        errors.push("Part Number is required.");
      return errors;
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.home {
  text-align: center;
}

.robot {
  height: 300px;
}

.get-started {
  padding-top: 20px;
  font-size: 25px;
}
</style>

