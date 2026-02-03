<template>
  <nav
    class="md:left-0 md:block md:fixed md:top-0 md:bottom-0 md:overflow-y-auto md:flex-row md:flex-nowrap md:overflow-hidden shadow-xl bg-white flex flex-wrap items-center justify-between relative md:w-64 z-10 py-4 px-6"
  >
    <div
      class="md:flex-col md:items-stretch md:min-h-full md:flex-nowrap px-0 flex flex-wrap items-center justify-between w-full mx-auto"
    >
      <!-- Toggler -->
      <button
        class="cursor-pointer text-black opacity-50 md:hidden px-3 py-1 text-xl leading-none bg-transparent rounded border border-solid border-transparent"
        type="button"
        @click="toggleCollapseShow('bg-white m-2 py-3 px-6')"
      >
        <i class="fas fa-bars"></i>
      </button>

      <!-- Brand -->
      <Link
        class="md:block text-left md:pb-2 text-blueGray-600 mr-0 inline-block whitespace-nowrap text-sm uppercase font-bold p-4 px-0"
        href="/"
      >
        <ApplicationLogo color="black" class="hidden sm:block" />
      </Link>

      <!-- User -->
      <ul class="md:hidden items-center flex flex-wrap list-none">
        <li class="inline-block relative">
          <notification-dropdown />
        </li>
        <li class="inline-block relative">
          <user-dropdown />
        </li>
      </ul>

      <!-- Collapse -->
      <div
        class="md:flex md:flex-col md:items-stretch md:opacity-100 md:relative md:mt-4 md:shadow-none shadow absolute top-0 left-0 right-0 z-40 overflow-y-auto overflow-x-hidden h-auto items-center flex-1 rounded"
        :class="collapseShow"
      >
        <!-- Collapse header -->
        <div class="md:min-w-full md:hidden block pb-4 mb-4 border-b border-solid border-blueGray-200">
          <div class="flex flex-wrap">
            <div class="w-6/12">
              <Link
                class="md:block text-left md:pb-2 text-blueGray-600 mr-0 inline-block whitespace-nowrap text-sm uppercase font-bold p-4 px-0"
                href="/"
              >
                <ApplicationLogo type="short" class="h-10" />
              </Link>
            </div>
            <div class="w-6/12 flex justify-end">
              <button
                type="button"
                class="cursor-pointer text-black opacity-50 md:hidden px-3 py-1 text-xl leading-none bg-transparent rounded border border-solid border-transparent"
                @click="toggleCollapseShow('hidden')"
              >
                <i class="fas fa-times"></i>
              </button>
            </div>
          </div>
        </div>

        <!-- Form -->
        <form class="mt-6 mb-4 md:hidden" @submit.prevent="searchProduct">
          <div class="mb-3 pt-0">
            <input
              type="text"
              v-model="form.keyword"
              placeholder="Search product..."
              class="border-0 px-3 py-2 h-12 border border-solid border-blueGray-500 placeholder-blueGray-300 text-blueGray-600 bg-white rounded text-base leading-snug shadow-none outline-none focus:outline-none w-full font-normal"
            />
          </div>
        </form>

        <hr class="my-4 md:min-w-full" />

        <!-- Navigation -->
        <ul class="md:flex-col md:min-w-full flex flex-col list-none">

          <!-- MAIN -->
          <li class="mt-2 mb-1 px-4 text-xs font-bold tracking-widest text-blueGray-400 uppercase">
            Main
          </li>
          <SidebarItem name="Dashboard" routeName="dashboard" icon="fas fa-tv" />

          <!-- SALES -->
          <li
            v-if="isAdmin || isSales"
            class="mt-6 mb-1 px-4 text-xs font-bold tracking-widest text-blueGray-400 uppercase"
          >
            Sales
          </li>
          <SidebarItem
            v-if="isAdmin || isSales"
            name="Sales"
            routeName="carts.index"
            icon="fas fa-shopping-cart"
          />
          <SidebarItem
            v-if="isAdmin || isSales"
            name="Orders"
            routeName="orders.index"
            icon="fas fa-database"
          />
          <SidebarItem
            v-if="isAdmin || isSales"
            name="Customers"
            routeName="customers.index"
            icon="fas fa-users"
          />

          <!-- INVENTORY -->
          <li
            v-if="isAdmin || isInventory"
            class="mt-6 mb-1 px-4 text-xs font-bold tracking-widest text-blueGray-400 uppercase"
          >
            Inventory
          </li>
          <SidebarItem
            v-if="isAdmin || isInventory"
            name="Products"
            routeName="products.index"
            icon="fas fa-shopping-bag"
          />
          <SidebarItem
            v-if="isAdmin || isInventory"
            name="Categories"
            routeName="categories.index"
            icon="fas fa-list"
          />
          <SidebarItem
            v-if="isAdmin || isInventory"
            name="Unit Types"
            routeName="unit-types.index"
            icon="fa fa-balance-scale"
          />
          <SidebarItem
            v-if="isAdmin || isInventory"
            name="Suppliers"
            routeName="suppliers.index"
            icon="fas fa-users-cog"
          />

          <!-- FINANCE -->
          <li
            v-if="isAdmin || isFinance"
            class="mt-6 mb-1 px-4 text-xs font-bold tracking-widest text-blueGray-400 uppercase"
          >
            Finance
          </li>
          <SidebarItem
            v-if="isAdmin || isFinance"
            name="Transactions"
            routeName="transactions.index"
            icon="fas fa-dollar-sign"
          />
          <SidebarItem
            v-if="isAdmin || isFinance"
            name="Expenses"
            routeName="expenses.index"
            icon="fas fa-book"
          />
          <SidebarItem
            v-if="isAdmin || isFinance"
            name="Salary"
            routeName="salaries.index"
            icon="fas fa-money-bill"
          />

          <!-- ADMINISTRATION -->
          <li
            v-if="isAdmin"
            class="mt-6 mb-1 px-4 text-xs font-bold tracking-widest text-blueGray-400 uppercase"
          >
            Administration
          </li>
          <SidebarItem
            v-if="isAdmin"
            name="Employees"
            routeName="employees.index"
            icon="fas fa-house-user"
          />
          <SidebarItem
            v-if="isAdmin"
            name="Settings"
            routeName="profile.edit"
            icon="fas fa-tools"
          />
        </ul>

        <hr class="my-4 md:min-w-full" />
      </div>
    </div>
  </nav>
</template>

<script setup>
import { ref, computed } from "vue";
import { Link, useForm, usePage } from "@inertiajs/vue3";

import NotificationDropdown from "@/Components/Dropdowns/NotificationDropdown.vue";
import UserDropdown from "@/Components/Dropdowns/UserDropdown.vue";
import SidebarItem from "@/Components/Sidebar/SidebarItem.vue";
import ApplicationLogo from "@/Components/ApplicationLogo.vue";

// Toggle responsive menu
const collapseShow = ref("hidden");
const toggleCollapseShow = (classes) => {
  collapseShow.value = classes;
};

// Search form
const form = useForm({ keyword: null });
const searchProduct = () => {
  form.get(route("carts.index"), { preserveScroll: true });
};

// Role-based visibility (safe default)
const page = usePage();

// Intenta leer un rol desde props; si no existe, se asume admin para no romper el sistema.
const userRole = computed(() => page.props.auth?.user?.role ?? "admin");

const isAdmin = computed(() => userRole.value === "admin");
const isSales = computed(() => userRole.value === "sales");
const isInventory = computed(() => userRole.value === "inventory");
const isFinance = computed(() => userRole.value === "finance");
</script>
