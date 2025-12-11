<script setup>
import AuthenticatedLayout from '@/Layouts/AuthenticatedLayout.vue';
import { router } from '@inertiajs/vue3';
import { Button } from '@/components/ui/button';
import { Input } from '@/components/ui/input';
import { Table, TableBody, TableCell, TableHead, TableHeader, TableRow } from '@/components/ui/table';
import { debounce } from 'lodash';
import { ref, watch } from 'vue';

const props = defineProps({ products: Object, filters: Object });
const search = ref(props.filters.search || '');

// Debounce search to prevent spamming the server
watch(search, debounce((value) => {
    router.get(route('products.index'), { search: value }, { preserveState: true, replace: true });
}, 300));

const deleteProduct = (id) => {
    if(confirm('Are you sure you want to delete this product?')) {
        router.delete(route('products.destroy', id));
    }
}
</script>

<template>
    <AuthenticatedLayout>
        <div class="p-6 bg-white shadow rounded-lg">
            <div class="flex justify-between mb-4">
                <Input v-model="search" placeholder="Search by Name or SKU..." class="max-w-sm" />
                <Button @click="router.visit(route('products.create'))">Add Product</Button>
            </div>

            <Table>
                <TableHeader>
                    <TableRow>
                        <TableHead>Image</TableHead>
                        <TableHead>Name</TableHead>
                        <TableHead>SKU</TableHead>
                        <TableHead>Price</TableHead>
                        <TableHead>Stock</TableHead>
                        <TableHead>Actions</TableHead>
                    </TableRow>
                </TableHeader>
                <TableBody>
                    <TableRow v-for="product in products.data" :key="product.id">
                        <TableCell>
                            <img :src="`/storage/${product.image_path}`" class="w-12 h-12 object-cover rounded" v-if="product.image_path" />
                        </TableCell>
                        <TableCell>{{ product.name }}</TableCell>
                        <TableCell>{{ product.sku }}</TableCell>
                        <TableCell>${{ product.price }}</TableCell>
                        <TableCell>{{ product.stock }}</TableCell>
                        <TableCell>
                            <Button variant="outline" size="sm" class="mr-2" @click="router.visit(route('products.edit', product.id))">Edit</Button>
                            <Button variant="destructive" size="sm" @click="deleteProduct(product.id)">Delete</Button>
                        </TableCell>
                    </TableRow>
                </TableBody>
            </Table>

        </div>
    </AuthenticatedLayout>
</template>
