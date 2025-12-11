<script setup>
import AuthenticatedLayout from '@/Layouts/AuthenticatedLayout.vue';
import { Head, useForm, Link } from '@inertiajs/vue3';
import { Input } from '@/components/ui/input';
import { Button } from '@/components/ui/button';
import { Label } from '@/components/ui/label';
import { Textarea } from '@/components/ui/textarea';
import { Card, CardHeader, CardTitle, CardContent } from '@/components/ui/card';

const props = defineProps({ product: Object });


const form = useForm({
    _method: 'PUT',
    name: props.product.name,
    sku: props.product.sku,
    price: props.product.price,
    stock: props.product.stock,
    description: props.product.description || '',
    image: null,
});

const handleFileChange = (event) => {
    form.image = event.target.files[0];
};

const submit = () => {
    form.post(route('products.update', props.product.id), {
        forceFormData: true,
    });
};
</script>

<template>
    <Head title="Edit Product" />

    <AuthenticatedLayout>
        <div class="max-w-2xl mx-auto p-6">
            <Card>
                <CardHeader>
                    <CardTitle>Edit Product: {{ product.name }}</CardTitle>
                </CardHeader>
                <CardContent>
                    <form @submit.prevent="submit" class="space-y-6">

                        <div class="space-y-2">
                            <Label for="name">Product Name</Label>
                            <Input id="name" v-model="form.name" />
                            <p v-if="form.errors.name" class="text-sm text-red-500">{{ form.errors.name }}</p>
                        </div>

                        <div class="space-y-2">
                            <Label for="sku">SKU</Label>
                            <Input id="sku" v-model="form.sku" />
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
                            <Label for="image">Update Image (Optional)</Label>

                            <div v-if="product.image_path" class="mb-2">
                                <p class="text-xs text-gray-500 mb-1">Current Image:</p>
                                <img :src="`/storage/${product.image_path}`" class="w-20 h-20 object-cover rounded border" />
                            </div>

                            <Input id="image" type="file" @change="handleFileChange" accept="image/*" />
                            <p v-if="form.errors.image" class="text-sm text-red-500">{{ form.errors.image }}</p>
                        </div>

                        <div class="flex justify-end gap-4">
                            <Link :href="route('products.index')">
                                <Button variant="outline" type="button">Cancel</Button>
                            </Link>
                            <Button type="submit" :disabled="form.processing">
                                {{ form.processing ? 'Saving...' : 'Update Product' }}
                            </Button>
                        </div>
                    </form>
                </CardContent>
            </Card>
        </div>
    </AuthenticatedLayout>
</template>
