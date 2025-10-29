<script setup>
import ContactForm from "@/components/ContactForm.vue";
import contactServices from "@/services/contact.services";
import { ref, onMounted } from "vue";
import { useRouter, useRoute } from "vue-router";

const props = defineProps({ id: { type: String, required: true } });
const router = useRouter();
const route = useRoute();
const contact = ref(null);
const message = ref("");

const getContact = async (id) => {
  try {
    contact.value = await contactServices.get(id);
  } catch (error) {
    router.push({
      name: "notfound",
      params: {
        pathMatch: route.path.split("/").slice(1),
      },
      query: route.query,
      hash: route.hash,
    });
  }
};

const updateContact = async (data) => {
  try {
    await contactServices.update(contact.value._id, data);
    alert("Liên hệ được cập nhật thành công.");
    router.push({ name: "contactbook" });
  } catch (error) {
    console.error(error);
  }
};
const deleteContact = async () => {
  if (confirm("Bạn muốn xóa Liên hệ này?")) {
    try {
      await contactServices.delete(contact.value._id);
      router.push({ name: "contactbook" });
    } catch (error) {
      console.log(error);
    }
  }
};

onMounted(() => {
  getContact(props.id);
  message.value = "";
});
</script>
<template>
  <div class="page" v-if="contact">
    <h4>Hiệu chỉnh Liên Hệ</h4>
    <ContactForm
      :contact="contact"
      @submit:contact="updateContact"
      @delete:contact="deleteContact"
    />
    <p>{{ message }}</p>
  </div>
</template>
