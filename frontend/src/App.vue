<template>
  <div class="container">
    <div class="form">
      <label for="entityType">Entity Type:</label>
      <select id="entityType" v-model="state.selectedEntityType">
        <option value="not choosed">Not Choosed</option>
        <option value="lead">Deal</option>
        <option value="contact">Contact</option>
        <option value="companie">Company</option>
      </select>
      <button
        @click="createEntity"
        :disabled="state.selectedEntityType == 'not choosed'"
      >
        Create
      </button>
    </div>
    <div class="results">
      <h2>Results</h2>
      <div class="entities-container">
        <div v-if="state.entities.length === 0" class="empty-state">
          No entities created yet.
        </div>
        <div
          v-else
          class="entity"
          v-for="(entity, index) in state.entities"
          :key="index"
        >
          <h3>{{ entity.name }}</h3>
          <p>ID: {{ entity.id }}</p>

          <button class="delete-button" @click="deleteEntity(index)">
            Delete
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import axios, { AxiosResponse } from "axios";
import { reactive } from "vue";

interface Entity {
  id: number;
  name: string;
}

const state = reactive({
  selectedEntityType: "not choosed",
  creatingEntity: false,
  entities: [] as Entity[],
});

async function createEntity() {
  const entityType = state.selectedEntityType;
  const entityData = [{ name: `New ${entityType}` }];

  const response: AxiosResponse<{ id: number }> = await axios.post(
    "http://localhost:1337/api/entities",
    {
      entityType: entityType,
      entityData: entityData,
    }
  );

  // Add created entity to results
  const entity = {
    id: response.data.id,
    name: `${entityType}`,
  };
  state.entities.push(entity);
}

function deleteEntity(index: number) {
  state.entities.splice(index, 1);
}
</script>

<style>
.container {
  padding-top: 50px;
  display: flex;
  flex-direction: column;
  justify-content: center;
}

.form {
  width: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  margin-bottom: 20px;
}

label {
  margin-right: 10px;
}

select {
  padding: 5px;
  margin-right: 10px;
  border-radius: 5px;
  background-color: #282828;
  color: #fff;
}

button {
  padding: 5px 10px;
  border-radius: 5px;
  background-color: #0099ff;
  color: #fff;
  border: none;
  cursor: pointer;
}

button:disabled {
  background-color: #aaa;
  cursor: not-allowed;
}

.results {
  display: flex;
  width: 100%;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}

h2 {
  margin-bottom: 20px;
  text-align: center;
}

h3 {
  width: 20%;
  margin: 10px 0;
  font-size: 18px;
}

p {
  margin: 0;
  font-size: 16px;
}

.entities-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 100%;
}

.entity {
  display: flex;
  justify-content: space-between;
  align-items: center;
  width: 80%;
  padding: 20px;
  margin: 10px;
  margin-bottom: 10px;
  background-color: #282828;
  border-radius: 5px;
  box-shadow: 2px 2px 5px rgba(255, 255, 255, 0.1);
}

.entity-details {
  display: flex;
  flex-direction: column;
  color: #fff;
}

.empty-state {
  text-align: center;
  font-size: 20px;
  font-style: italic;
  color: #aaa;
  margin-top: 50px;
}
</style>
