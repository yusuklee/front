<template>
  <div class="container">
    <div class="d-flex align-items-center mb-3">
      <h3 class="mb-0">회원 목록</h3>
      <span class="badge badge-primary ml-2">{{ filtered.length }}명</span>

      <div class="ml-auto d-flex">
        <input
          v-model.trim="q"
          class="form-control mr-2"
          placeholder="이름/주소 검색"
        />
        <button
          class="btn btn-outline-primary"
          :disabled="loading"
          @click="reload"
        >
          새로고침
        </button>
      </div>
    </div>

    <div
      v-if="alert.show"
      :class="`alert alert-${alert.type} py-2`"
      role="alert"
    >
      {{ alert.msg }}
    </div>

    <div class="table-responsive" v-if="filtered.length">
      <table class="table table-striped table-hover">
        <thead class="thead-light">
          <tr>
            <th class="nowrap">ID</th>
            <th>이름</th>
            <th class="nowrap">우편번호</th>
            <th>도로명</th>
            <th>상세주소</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="m in filtered" :key="m.id ?? m.name ?? JSON.stringify(m)">
            <td class="nowrap">{{ m.id ?? '' }}</td>
            <td>{{ m.name ?? '' }}</td>
            <td class="nowrap">{{ (m.address?.zip) ?? '' }}</td>
            <td>{{ (m.address?.addr1) ?? '' }}</td>
            <td>{{ (m.address?.addr2) ?? '' }}</td>
          </tr>
        </tbody>
      </table>
    </div>

    <p class="text-muted" v-else>표시할 회원이 없습니다.</p>
  </div>
</template>

<script>
export default {
  name: "MemberList",
  data() {
    return {
      API_URL: "/member/show",
      rows: [],
      q: "",
      loading: false,
      alert: { show: false, type: "info", msg: "" },
    };
  },
  computed: {
    filtered() {
      const query = (this.q || "").toLowerCase();
      if (!query) return this.rows;

      const has = (t, q) => {
        t = (t == null ? "" : String(t)).toLowerCase();
        return t.includes(q);
      };

      return this.rows.filter((m) => {
        const a = m.address || {};
        return (
          has(m.id, query) ||
          has(m.name, query) ||
          has(a.zip, query) ||
          has(a.addr1, query) ||
          has(a.addr2, query)
        );
      });
    },
  },
  methods: {
    setAlert(msg, type = "info") {
      this.alert = { show: true, type, msg };
    },
    hideAlert() {
      this.alert.show = false;
    },
    async loadMembers() {
      this.setAlert("불러오는 중...");
      this.loading = true;
      try {
        const res = await fetch(this.API_URL, {
          headers: { Accept: "application/json" },
        });
        if (!res.ok) throw new Error(`HTTP ${res.status}`);
        const data = await res.json();
        this.rows = Array.isArray(data) ? data : [];
        this.hideAlert();
      } catch (e) {
        console.error(e);
        this.setAlert(
          "목록을 불러오지 못했습니다. 서버 상태를 확인하세요.",
          "danger"
        );
        this.rows = [];
      } finally {
        this.loading = false;
      }
    },
    reload() {
      this.q = "";
      this.loadMembers();
    },
  },
  mounted() {
    this.loadMembers();
  },
};
</script>

<style scoped>
body {
  padding: 24px;
}
.nowrap {
  white-space: nowrap;
}
</style>