<template>
  <div class="min-h-screen bg-gray-100">
    <header class="bg-white shadow flex justify-between items-center">
      <div class="max-w-7xl mx-auto py-6 px-4 sm:px-6 lg:px-8">
        <h1 class="text-3xl font-bold text-gray-900">Job Board</h1>
      </div>
      <div class="flex items-center mr-8 relative">
        <font-awesome-icon
          :icon="['fas', 'cog']"
          class="text-2xl text-gray-700 cursor-pointer mr-6"
        />
        <span
          v-if="hasNotification"
          class="absolute top-0 right-4 h-3 w-3 bg-red-500 rounded-full"
        ></span>
        <div class="relative" @click="toggleUserMenu">
          <font-awesome-icon
            :icon="['fas', 'user']"
            class="text-2xl text-gray-700 cursor-pointer"
          />
          <div
            v-if="isUserMenuOpen"
            class="absolute right-0 mt-2 w-48 bg-white border rounded-lg shadow-lg"
          >
            <a href="#" class="block px-4 py-2 text-gray-800 hover:bg-gray-100">Profile</a>
            <a href="#" class="block px-4 py-2 text-gray-800 hover:bg-gray-100">Settings</a>
            <a href="#" class="block px-4 py-2 text-gray-800 hover:bg-gray-100">Logout</a>
          </div>
        </div>
      </div>
    </header>
    <main>
      <div class="max-w-7xl mx-auto py-6 sm:px-6 lg:px-8">
        <div class="px-4 py-6 sm:px-0">
          <div class="border-4 border-dashed border-gray-200 rounded-lg h-auto p-4">
            <!-- Search and Filters -->
            <div class="mb-6 flex flex-col md:flex-row gap-4">
              <input
                v-model="searchQuery"
                class="w-full px-4 py-2 border rounded-lg"
                type="text"
                placeholder="Search for jobs..."
              />
              <select
                v-model="selectedLocation"
                class="w-full md:w-1/3 px-4 py-2 border rounded-lg"
              >
                <option value="">All Locations</option>
                <option v-for="location in locations" :key="location">{{ location }}</option>
              </select>
              <select v-model="selectedJobType" class="w-full md:w-1/3 px-4 py-2 border rounded-lg">
                <option value="">All Job Types</option>
                <option v-for="type in jobTypes" :key="type">{{ type }}</option>
              </select>
            </div>
            <!-- Job Board Content -->
            <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
              <div
                v-for="job in paginatedJobs"
                :key="job.id"
                class="bg-white p-6 rounded-lg shadow-md"
              >
                <h2 class="text-xl font-bold mb-2">{{ job.title }}</h2>
                <p class="text-gray-700 mb-4">{{ job.company }}</p>
                <p class="text-gray-500 mb-4">{{ job.location }}</p>
                <p class="text-gray-500">{{ job.description }}</p>
                <button class="mt-4 px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600">
                  Apply
                </button>
              </div>
            </div>
            <!-- Pagination -->
            <div class="mt-6 flex justify-center">
              <button
                @click="previousPage"
                :disabled="currentPage === 1"
                class="px-4 py-2 bg-gray-300 rounded-lg hover:bg-gray-400"
              >
                Previous
              </button>
              <button
                @click="nextPage"
                :disabled="currentPage === totalPages"
                class="ml-2 px-4 py-2 bg-gray-300 rounded-lg hover:bg-gray-400"
              >
                Next
              </button>
            </div>
          </div>
        </div>
      </div>
    </main>
  </div>
</template>

<script>
export default {
  data() {
    return {
      searchQuery: '',
      selectedLocation: '',
      selectedJobType: '',
      currentPage: 1,
      itemsPerPage: 6,
      isUserMenuOpen: false,
      hasNotification: true,
      jobs: [
        {
          id: 1,
          title: 'Frontend Developer',
          company: 'Tech Corp',
          location: 'New York',
          type: 'Full-time',
          description: 'Develop modern web applications.'
        },
        {
          id: 2,
          title: 'Backend Developer',
          company: 'Data Systems',
          location: 'San Francisco',
          type: 'Part-time',
          description: 'Build and maintain server-side logic.'
        },
        {
          id: 3,
          title: 'Fullstack Developer',
          company: 'Web Solutions',
          location: 'Remote',
          type: 'Contract',
          description: 'Work on both frontend and backend tasks.'
        },
        {
          id: 4,
          title: 'DevOps Engineer',
          company: 'Cloud Services',
          location: 'Austin',
          type: 'Full-time',
          description: 'Maintain and improve cloud infrastructure.'
        },
        {
          id: 5,
          title: 'UI/UX Designer',
          company: 'Creative Inc.',
          location: 'Los Angeles',
          type: 'Part-time',
          description: 'Design user-friendly interfaces and experiences.'
        },
        {
          id: 6,
          title: 'Project Manager',
          company: 'Enterprise Solutions',
          location: 'Chicago',
          type: 'Contract',
          description: 'Lead and manage software development projects.'
        },
        {
          id: 7,
          title: 'Data Analyst',
          company: 'Analytics Co.',
          location: 'Seattle',
          type: 'Full-time',
          description: 'Analyze and interpret complex data sets.'
        },
        {
          id: 7,
          title: 'Data Analyst',
          company: 'Analytics Co.',
          location: 'Seattle',
          type: 'Full-time',
          description: 'Analyze and interpret complex data sets.'
        },
        {
          id: 7,
          title: 'Data Analyst',
          company: 'Analytics Co.',
          location: 'Seattle',
          type: 'Full-time',
          description: 'Analyze and interpret complex data sets.'
        }
      ],
      locations: [
        'New York',
        'San Francisco',
        'Remote',
        'Austin',
        'Los Angeles',
        'Chicago',
        'Seattle'
      ],
      jobTypes: ['Full-time', 'Part-time', 'Contract']
    }
  },
  computed: {
    filteredJobs() {
      return this.jobs.filter((job) => {
        return (
          (job.title.toLowerCase().includes(this.searchQuery.toLowerCase()) ||
            job.company.toLowerCase().includes(this.searchQuery.toLowerCase()) ||
            job.description.toLowerCase().includes(this.searchQuery.toLowerCase())) &&
          (this.selectedLocation === '' || job.location === this.selectedLocation) &&
          (this.selectedJobType === '' || job.type === this.selectedJobType)
        )
      })
    },
    paginatedJobs() {
      const start = (this.currentPage - 1) * this.itemsPerPage
      const end = this.currentPage * this.itemsPerPage
      return this.filteredJobs.slice(start, end)
    },
    totalPages() {
      return Math.ceil(this.filteredJobs.length / this.itemsPerPage)
    }
  },
  methods: {
    toggleUserMenu() {
      this.isUserMenuOpen = !this.isUserMenuOpen
    },
    nextPage() {
      if (this.currentPage < this.totalPages) {
        this.currentPage++
      }
    },
    previousPage() {
      if (this.currentPage > 1) {
        this.currentPage--
      }
    }
  }
}
</script>
