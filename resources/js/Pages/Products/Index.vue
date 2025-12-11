<script setup>
import AuthenticatedLayout from '@/Layouts/AuthenticatedLayout.vue';
import Pagination from '@/Components/Pagination.vue'; // Import Pagination
import {Head, router} from '@inertiajs/vue3';
import { Button } from '@/components/ui/button';
import { Input } from '@/components/ui/input';
import { Table, TableBody, TableCell, TableHead, TableHeader, TableRow } from '@/components/ui/table';
import {
    Dialog,
    DialogContent,
    DialogDescription,
    DialogFooter,
    DialogHeader,
    DialogTitle,
} from '@/components/ui/dialog'; // Import ShadCN Dialog components
import { debounce } from 'lodash';
import { ref, watch } from 'vue';

const props = defineProps({ products: Object, filters: Object});
const search = ref(props.filters.search || '');

// --- Search Logic ---
watch(search, debounce((value) => {
    router.get(route('products.index'), {search: value}, {preserveState: true, replace: true});
}, 300));

// --- Delete Dialog Logic ---
const isDialogOpen = ref(false);
const productToDelete = ref(null);

// Open the modal and set the product to be deleted
const confirmDelete = (product) => {
    productToDelete.value = product;
    isDialogOpen.value = true;
};

// Execute the deletion
const executeDelete = () => {
    if (productToDelete.value) {
        router.delete(route('products.destroy', productToDelete.value.id), {
            onSuccess: () => {
                // Close modal and reset state on success
                isDialogOpen.value = false;
                productToDelete.value = null;
            }
        });
    }
};
</script>

<template>
    <Head title="Product Inventory List" />
    <AuthenticatedLayout>
        <div class="p-6 bg-white shadow rounded-lg space-y-4">
            <div>
                <h2 class="text-xl font-semibold leading-tight text-gray-800 text-left">
                    Product Inventory List
                </h2>
            </div>
            <div class="flex justify-between items-center">
                <Input v-model="search" placeholder="Search by Name or SKU..." class="max-w-sm"/>
                <Button @click="router.visit(route('products.create'))">Add Product</Button>
            </div>

            <div class="border rounded-md">
                <Table>
                    <TableHeader>
                        <TableRow>
                            <TableHead>Image</TableHead>
                            <TableHead>Name</TableHead>
                            <TableHead>SKU</TableHead>
                            <TableHead>Price</TableHead>
                            <TableHead>Stock</TableHead>
                            <TableHead class="text-right">Actions</TableHead>
                        </TableRow>
                    </TableHeader>
                    <TableBody>
                        <TableRow v-for="product in products.data" :key="product.id">
                            <TableCell>
                                <img
                                    v-if="product.image_path"
                                    :src="`/storage/${product.image_path}`"
                                    class="w-10 h-10 object-cover rounded"
                                    alt="Product Image"
                                />
                                <span v-else class="text-gray-400 text-xs">No Image</span>
                            </TableCell>
                            <TableCell class="font-medium">{{ product.name }}</TableCell>
                            <TableCell>{{ product.sku }}</TableCell>
                            <TableCell>${{ product.price }}</TableCell>
                            <TableCell>
                                <span :class="product.stock < 10 ? 'text-red-500 font-bold' : ''">
                                    {{ product.stock }}
                                </span>
                            </TableCell>
                            <TableCell class="text-right">
                                <Button variant="outline" size="sm" class="mr-2"
                                        @click="router.visit(route('products.edit', product.id))">
                                    Edit
                                </Button>
                                <Button variant="destructive" size="sm" @click="confirmDelete(product)">
                                    Delete
                                </Button>
                            </TableCell>
                        </TableRow>

                        <TableRow v-if="products.data.length === 0">
                            <TableCell colspan="6" class="h-24 text-center">
                                No products found.
                            </TableCell>
                        </TableRow>
                    </TableBody>
                </Table>
            </div>

            <div class="mt-4">
                <Pagination :links="products.links"/>
            </div>

            <Dialog :open="isDialogOpen" @update:open="isDialogOpen = $event">
                <DialogContent>
                    <DialogHeader>
                        <DialogTitle>Are you sure?</DialogTitle>
                        <DialogDescription>
                            This action cannot be undone. This will permanently delete
                            <span class="font-bold text-black">{{ productToDelete?.name }}</span>
                            from the inventory.
                        </DialogDescription>
                    </DialogHeader>
                    <DialogFooter>
                        <Button variant="outline" @click="isDialogOpen = false">Cancel</Button>
                        <Button variant="destructive" @click="executeDelete">Delete</Button>
                    </DialogFooter>
                </DialogContent>
            </Dialog>

        </div>
    </AuthenticatedLayout>
</template>
