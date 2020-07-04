
<template>
  <div class="list row">
    <div class="col-md-8">
      <div class="input-group mb-3">
        <input
          type="text"
          class="form-control"
          placeholder="Search by title"
          v-model="title"
        />
        <div class="input-group-append">
          <button
            class="btn btn-outline-secondary"
            type="button"
            @click="searchTitle"
          >
            Search
          </button>
        </div>
      </div>
    </div>
    <div class="col-md-6">
      <h4>Tutorials List</h4>
      <ul class="list-group">
        <li
          class="list-group-item"
          :class="{ active: index == currentIndex }"
          v-for="(tutorial, index) in tutorials"
          :key="index"
          @click="setActiveTutorial(tutorial, index)"
        >
          {{ tutorial.title }}
        </li>
      </ul>

      <button class="m-3 btn btn-sm btn-danger" @click="removeAllTutorials">
        Remove All
      </button>
    </div>
    <div class="col-md-6">
      <div v-if="currentTutorial">
        <h4>Tutorial</h4>
        <div>
          <label><strong>Title:</strong></label> {{ currentTutorial.title }}
        </div>
        <div>
          <label><strong>Description:</strong></label>
          {{ currentTutorial.description }}
        </div>
        <div>
          <label><strong>Status:</strong></label>
          {{ currentTutorial.published ? "Published" : "Pending" }}
        </div>

        <a
          class="badge badge-warning"
          :href="'/tutorials/' + currentTutorial.id"
        >
          Edit
        </a>
      </div>
      <div v-else>
        <br />
        <p>Please click on a Tutorial...</p>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import {Vue, Component} from 'vue-property-decorator'
import TutorialDataService from "../services/TutorialDataService";

@Component
export default class TutorialsList extends Vue {
	private tutorials: any[] = [];
	private currentTutorial: any = null;
	private currentIndex: number = -1;
	private title: string = "";

	retrieveTutorials() {
		TutorialDataService.getAll().then(res => {
			this.tutorials = res.data;
			console.log(res.data);
		}).catch(e => {
			console.log(e);
		});
	}

	refreshList() {
		this.retrieveTutorials();
		this.currentTutorial = null;
		this.currentIndex = -1;
	}

	setActiveTutorial(tutorial: any, index: number) {
		this.currentTutorial = tutorial;
		this.currentIndex = index;
	}

	removeAllTutorials() {
		TutorialDataService.deleteAll().then(res => {
			console.log(res.data);
			this.refreshList();
		}).catch(e => {
			console.log(e);
		});
	}

	searchTitle() {
		TutorialDataService.findByTitle(this.title).then(res => {
			this.tutorials = res.data;
			console.log(res.data);
		}).catch(e => {
			console.log(e);
		});
	}

	mounted() {
		this.retrieveTutorials();
	}
}
</script>

<style scoped>
.list {
	text-align: left;
	max-width: 750px;
	margin: auto;
}
</style>
