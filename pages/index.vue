<template>
  <div class="report">
    <!-- Title -->
    <h2 class="title">
      <div class="text">
        <span class="orange">lost</span>&<span class="green">found</span>
      </div>
      <div class="query">
        <b-form-input v-model="query" placeholder="Cari laporan"></b-form-input>
      </div>
    </h2>
    <!-- Loading -->
    <div v-if="!reportsLoaded">
      <b-spinner class="spinner"></b-spinner>
    </div>
    <!-- List Reports -->
    <div v-else>
      <div
        data-aos="fade-up"
        :class="'report-item ' + classReport(report)"
        v-for="report in reports"
        :key="report.id"
      >
        <div class="description">
          {{ report.description }}
        </div>
        <div class="date">
          <small>
            {{ new Date(report.created_date).toLocaleString() }} WIB
          </small>
        </div>
      </div>
    </div>
    <!-- Input new -->
    <div class="input">
      <div class="floating" @click="showModalAdd">
        <i class="icofont-plus"></i>
      </div>
      <b-modal
        ref="modal-add"
        centered
        ok-title="Submit"
        title="Tambah Laporan"
        @ok.prevent="submitAdd"
      >
        <b-form-group label="Kategori">
          <b-form-select
            v-model="form.category"
            :options="categories"
          ></b-form-select>
        </b-form-group>
        <b-form-group label="Deskripsi">
          <b-form-textarea
            :state="form.description ? form.description.length <= 255 : null"
            rows="5"
            v-model="form.description"
          >
          </b-form-textarea>
          <small class="float-right" v-if="form.description"
            >({{ form.description.length }}/255)</small
          >
          <small>
            Deskripsikan benda yang hilang/ditemukan dengan ringkas dan jelas
          </small>
        </b-form-group>
        <Loading :visible="modalLoading.visible" />
      </b-modal>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Home',
  data() {
    return {
      reports: [],
      reportsLoaded: false,
      categories: ['lost', 'found'],
      form: {
        category: null,
        description: null,
      },
      modalLoading: {
        visible: false,
      },
      query: '',
      isTyping: false,
    }
  },
  watch: {
    query() {
      if (!this.isTyping) {
        setTimeout(() => {
          this.loadReports()
          this.isTyping = false
        }, 1000) // 1 sec delay
      }
      this.isTyping = true
    },
  },
  mounted() {
    this.loadReports()
  },
  methods: {
    showModalAdd() {
      this.$refs['modal-add'].show()
    },
    hideModalAdd() {
      this.$refs['modal-add'].hide()
      this.form.category = null
      this.form.description = null
    },
    classReport(report) {
      return report.category
    },
    loadReports() {
      this.reports = []
      this.reportsLoaded = false
      this.$axios
        .get(`${process.env.baseAPI}/report`, {
          params: {
            q: this.query,
          },
        })
        .then((res) => {
          this.reports = res.data
        })
        .finally(() => {
          this.reportsLoaded = true
        })
    },
    submitAdd() {
      this.modalLoading.visible = true
      this.$axios
        .post(`${process.env.baseAPI}/report`, this.form)
        .then((res) => {
          console.log(res.data)
        })
        .catch((err) => {
          alert('Gagal menambahkan laporan')
        })
        .finally(() => {
          this.modalLoading.visible = false
          this.hideModalAdd()
          this.loadReports()
        })
    },
  },
  computed: {},
}
</script>

<style>
.report {
  min-width: 300px;
  min-height: 100vh;
  background-image: url('~assets/bg.jpg');
  background-repeat: repeat-y;
  padding: 2em;
  display: flex;
  flex-direction: column;
  position: relative;
}
.orange {
  color: orange;
}
.green {
  color: lightgreen;
}
.title {
  color: lightgray;
  display: flex;
}
.title .query {
  flex: 1;
  margin-left: 1em;
}
.report-item {
  margin-top: 1em;
  max-width: 80%;
  padding: 0.7em;
  border-radius: 0.2em;
  font-weight: 500;
  line-height: 1.6;
  background: white;
  font-size: 1.2em;
  border-width: 6px;
  border-style: solid;
}
.date {
  text-align: right;
  color: grey;
}
.lost {
  border-color: #ffa500;
  text-align: left;
}
.found {
  border-color: #90ee90;
  margin-left: 20%;
}
.input {
  margin: auto 0 0 auto;
  position: fixed;
  right: 2em;
  bottom: 2em;
}
.floating {
  width: 50px;
  height: 50px;
  background: rgb(255, 165, 0);
  background: linear-gradient(
    25deg,
    rgba(255, 165, 0, 1) 0%,
    rgba(144, 238, 144, 1) 100%
  );
  display: flex;
  justify-content: center;
  align-items: center;
  border-radius: 50%;
  cursor: pointer;
}
.floating:hover {
  background: linear-gradient(
    115deg,
    rgba(255, 165, 0, 1) 0%,
    rgba(144, 238, 144, 1) 100%
  );
}
i {
  font-size: 2em;
  color: #171717;
}
.spinner {
  border-color: lightgreen;
  border-right-color: orange;
  width: 3rem;
  height: 3rem;
}
@media (max-width: 480px) {
  .title {
    flex-direction: column;
  }
  .title .query {
    margin: .5em 0 0;
  }
}
</style>
