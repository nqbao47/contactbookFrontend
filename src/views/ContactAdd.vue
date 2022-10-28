<template>
    <div v-if="contact" class="page">
        <h4 v-if="addCont">Thêm Liên Hệ</h4>
        <ContactForm 
            :contact="contact" 
            @submit:contact="onUpdateContact"     
        />
        <p>{{ message }}</p>
    </div>
</template>
<script>
import ContactForm from '@/components/ContactForm.vue';
import { contactService } from '@/services/contact.service';
export default {
    components: {
        ContactForm,
    },
    props: {
        addCont: { type: Boolean, required: false },
    },
    data() {
        return {
            contact: null,
            message: '',
        };
    },
    methods: {
        async getContact(id) {
            try {
                if (id) {
                this.contact = await contactService.get(id);
            } else {
                this.contact = {
                    name: "",
                    email: "",
                    address: "",
                    phone: "",
                    favorite: false,
          };
        }
      } catch (error) {
                console.log(error);
                // Redirect to NotFound page and keep URL intact
                this.$router.push({
                    name: 'notfound',
                    params: {
                        pathMatch: this.$route.path.split('/').slice(1)
                    },
                    query: this.$route.query,
                    hash: this.$route.hash,
                });
            }
        },
        async onUpdateContact(contact) {
            try {
                await contactService.create(contact);
                this.message = "Thêm Liên hệ thành công";
            } catch (error) {
                console.log(error);
            }
        },
        async onDeleteContact(id) {
            if (confirm('Bạn muốn xóa Liên hệ này?')) {
                try {
                    await contactService.delete(id);
                    this.$router.push({ name: 'contactbook' });
                } catch (error) {
                    console.log(error);
                }
            }
        },
    },
    created() {
        this.getContact(this.contactId);
        this.message = '';
    },
};
</script>