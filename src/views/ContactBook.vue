<script setup>
import ContactCard from "@/components/ContactCard.vue";
import contactServices from "@/services/contact.services";
import InputSearch from "@/components/InputSearch.vue";
import ContactList from "@/components/ContactList.vue";
import { computed, reactive, ref, watch, onMounted } from "vue";
import { useRouter } from "vue-router";
import { param } from "jquery";
const router = useRouter();
const contacts = ref([]);
const activeIndex = ref(-1);
const searchText = ref("");
watch(searchText, () => {
  activeIndex.value = -1;
});
const contactStrings = computed(() => {
  return contacts.value.map((contact) => {
    const { name, email, address, phone } = contact;
    return [name, email, address, phone].join("");
  });
});
const filteredContacts = computed(() => {
  if (!searchText.value) {
    return contacts.value;
  }
  return contacts.value.filter((contact, index) => {
    const contactString = contactStrings.value[index].toLocaleLowerCase();
    return contactString.includes(searchText.value.toLocaleLowerCase());
  });
});

const activeContact = computed(() => {
  if (activeIndex.value < 0) return null;
  return filteredContacts.value[activeIndex.value];
});

const filteredContactsCount = computed(() => {
  return filteredContacts.value.length;
});

const retrieveContacts = async () => {
  try {
    contacts.value = await contactServices.getAll();
  } catch (error) {
    console.error(error);
  }
};

const refreshList = () => {
  retrieveContacts();
  activeIndex.value = -1;
};
const removeAllContacts = async () => {
  if (confirm("Bạn có muốn xóa tất cả các liên hệ ? ")) {
    try {
      await contactServices.deleteAll();
      refreshList();
    } catch (error) {
      console.error(error);
    }
  }
};

const goToAddContact = () => {
  return router.push({ name: "contact.add" });
};
onMounted(() => {
  refreshList();
});
</script>

<template>
  <div class="page row">
    <div class="col-md-10">
      <InputSearch v-model="searchText" />
    </div>
    <div class="mt-3 col-md-6">
      <h4>
        Danh bạ
        <i class="fas fa-address-book"></i>
      </h4>
      <ContactList
        v-if="filteredContactsCount > 0"
        :contacts="filteredContacts"
        v-model:activeIndex="activeIndex"
      />
      <p v-else>Không có liên hệ nào.</p>
      <div class="mt-3 row justify-content-around align-items-center">
        <button class="btn btn-sm btn-primary" @click="refreshList">
          <i class="fas fa-redo"></i> Làm mới
        </button>
        <button class="btn btn-sm btn-success" @click="goToAddContact">
          <i class="fas fa-plus"></i> Thêm mới
        </button>
        <button class="btn btn-sm btn-danger" @click="removeAllContacts">
          <i class="fas fa-trash"></i> Xóa tất cả
        </button>
      </div>
    </div>
    <div class="mt-3 col-md-6">
      <div v-if="activeContact">
        <h4>
          Chi tiết Liên hệ
          <i class="fas fa-address-card"></i>
        </h4>
        <ContactCard :contact="activeContact" />
        <router-link
          :to="{ name: 'contact.edit', params: { id: activeContact._id } }"
        >
          <span class="mt-2 badge badge-warning">
            <i class="fas fa-edit"></i> Hiệu chỉnh</span
          >
        </router-link>
      </div>
    </div>
  </div>
</template>
<style scoped>
.page {
  text-align: left;
  max-width: 750px;
}
</style>
