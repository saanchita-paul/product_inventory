<script setup>
import AuthenticatedLayout from '@/Layouts/AuthenticatedLayout.vue';
import { Head, useForm, Link } from '@inertiajs/vue3';
import { Input } from '@/components/ui/input';
import { Button } from '@/components/ui/button';
import { Label } from '@/components/ui/label';
import { Textarea } from '@/components/ui/textarea';
import { Card, CardHeader, CardTitle, CardContent } from '@/components/ui/card';
import {ref} from "vue";
import {runInContext as file} from "lodash";

const form = useForm({
    name: '',
    sku: '',
    price: '',
    stock: '',
    description: '',
    image: null,
});

const imageError = ref(null);

const handleFileChange = (event) => {
    form.image = event.target.files[0];
    imageError.value = null;
    form.clearErrors('image');
    if (file) {
        if (file.size > 2 * 1024 * 1024) {
            imageError.value = "The file is too large. Max size is 2MB.";
            event.target.value = null;
            form.image = null;
            return;
        }
        form.image = file;
    }
};

const submit = () => {
    form.post(route('products.store'), {
        forceFormData: true,
    });
};
</script>

<template>
    <Head title="Create Product" />

    <AuthenticatedLayout>
        <div class="max-w-2xl mx-auto p-6">
            <Card>
                <CardHeader>
                    <CardTitle>Create New Product</CardTitle>
                </CardHeader>
                <CardContent>
                    <form @submit.prevent="submit" class="space-y-6">

                        <div class="space-y-2">
                            <Label for="name">Product Name</Label>
                            <Input id="name" v-model="form.name" placeholder="e.g. Wireless Mouse" />
                            <p v-if="form.errors.name" class="text-sm text-red-500">{{ form.errors.name }}</p>
                        </div>

                        <div class="space-y-2">
                            <Label for="sku">SKU</Label>
                            <Input id="sku" v-model="form.sku" placeholder="Unique ID" />
                            <p v-if="form.errors.sku" class="text-sm text-red-500">{{ form.errors.sku }}</p>
                        </div>

                        <div class="grid grid-cols-2 gap-4">
                            <div class="space-y-2">
                                <Label for="price">Price ($)</Label>
                                <Input id="price" type="number" step="0.01" v-model="form.price" />
                                <p v-if="form.errors.price" class="text-sm text-red-500">{{ form.errors.price }}</p>
                            </div>

                            <div class="space-y-2">
                                <Label for="stock">Stock Quantity</Label>
                                <Input id="stock" type="number" v-model="form.stock" />
                                <p v-if="form.errors.stock" class="text-sm text-red-500">{{ form.errors.stock }}</p>
                            </div>
                        </div>

                        <div class="space-y-2">
                            <Label for="description">Description</Label>
                            <Textarea id="description" v-model="form.description" />
                            <p v-if="form.errors.description" class="text-sm text-red-500">{{ form.errors.description }}</p>
                        </div>

                        <div class="space-y-2">
                            <Label for="image">Product Image</Label>
                            <Input id="image" type="file" @change="handleFileChange" accept="image/*" />

                            <p v-if="form.errors.image" class="text-sm text-red-500">{{ form.errors.image }}</p>

                            <p v-if="imageError" class="text-sm text-red-500">{{ imageError }}</p>
                        </div>

                        <div class="flex justify-end gap-4">
                            <Link :href="route('products.index')">
                                <Button variant="outline" type="button">Cancel</Button>
                            </Link>
                            <Button type="submit" :disabled="form.processing">
                                {{ form.processing ? 'Saving...' : 'Create Product' }}
                            </Button>
                        </div>
                    </form>
                </CardContent>
            </Card>
        </div>
    </AuthenticatedLayout>
</template>
