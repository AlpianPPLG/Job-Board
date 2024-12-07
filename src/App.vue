<template>
  <div :class="['min-h-screen', isDarkMode ? 'bg-gray-900' : 'bg-gray-100']">
    <header
      :class="['shadow flex justify-between items-center', isDarkMode ? 'bg-gray-800' : 'bg-white']"
    >
      <div class="max-w-7xl mx-auto py-6 px-4 sm:px-6 lg:px-8">
        <h1 :class="['text-3xl font-bold', isDarkMode ? 'text-white' : 'text-gray-900']">
          Job Board
        </h1>
      </div>
      <div class="flex items-center mr-8 relative">
        <font-awesome-icon
          :icon="['fas', 'cog']"
          class="text-2xl cursor-pointer mr-6"
          :class="isDarkMode ? 'text-gray-300' : 'text-gray-700'"
        />
        <span
          v-if="hasNotification"
          class="absolute top-0 right-4 h-3 w-3 bg-red-500 rounded-full"
        ></span>
        <div class="relative" @click="toggleUserMenu">
          <font-awesome-icon
            :icon="['fas', 'user']"
            class="text-2xl cursor-pointer"
            :class="isDarkMode ? 'text-gray-300' : 'text-gray-700'"
          />
          <div
            v-if="isUserMenuOpen"
            :class="[
              'absolute right-0 mt-2 w-48 border rounded-lg shadow-lg',
              isDarkMode ? 'bg-gray-800' : 'bg-white'
            ]"
          >
            <a
              :class="[
                'block px-4 py-2',
                isDarkMode ? 'text-gray-300 hover:bg-gray-700' : 'text-gray-800 hover:bg-gray-100'
              ]"
              href="#"
              >Profile</a
            >
            <a
              :class="[
                'block px-4 py-2',
                isDarkMode ? 'text-gray-300 hover:bg-gray-700' : 'text-gray-800 hover:bg-gray-100'
              ]"
              href="#"
              >Settings</a
            >
            <a
              :class="[
                'block px-4 py-2',
                isDarkMode ? 'text-gray-300 hover:bg-gray-700' : 'text-gray-800 hover:bg-gray-100'
              ]"
              href="#"
              >Logout</a
            >
          </div>
        </div>
        <button
          @click="toggleDarkMode"
          class="ml-4 p-2 rounded bg-blue-500 text-white hover:bg-blue-600"
        >
          Toggle Dark Mode
        </button>
      </div>
    </header>
    <main>
      <div class="max-w-7xl mx-auto py-6 sm:px-6 lg:px-8">
        <div class="px-4 py-6 sm:px-0">
          <div
            class="border-4 border-dashed"
            :class="isDarkMode ? 'border-gray-700' : 'border-gray-200'"
          >
            <!-- Search and Filters -->
            <div class="mb-6 flex flex-col md:flex-row gap-4">
              <input
                v-model="searchQuery"
                class="w-full px-4 py-2 border rounded-lg"
                :class="isDarkMode ? 'bg-gray-800 text-white border-gray-600' : 'border'"
                type="text"
                placeholder="Search for jobs..."
              />
              <select
                v-model="selectedLocation"
                class="w-full md:w-1/3 px-4 py-2 border rounded-lg"
                :class="isDarkMode ? 'bg-gray-800 text-white border-gray-600' : 'border'"
              >
                <option value="">All Locations</option>
                <option v-for="location in locations" :key="location">{{ location }}</option>
              </select>
              <select
                v-model="selectedJobType"
                class="w-full md:w-1/3 px-4 py-2 border rounded-lg"
                :class="isDarkMode ? 'bg-gray-800 text-white border-gray-600' : 'border'"
              >
                <option value="">All Job Types</option>
                <option v-for="type in jobTypes" :key="type">{{ type }}</option>
              </select>
            </div>
            <!-- Job Board Content -->
            <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
              <div
                v-for="job in paginatedJobs"
                :key="job.id"
                class="p-6 rounded-lg shadow-md"
                :class="isDarkMode ? 'bg-gray-800 text-white' : 'bg-white text-gray-900'"
              >
                <h2 class="text-xl font-bold mb-2">{{ job.title }}</h2>
                <p class="mb-4">{{ job.company }}</p>
                <p class="mb-4">{{ job.location }}</p>
                <p>{{ job.description }}</p>
                <button class="mt-4 px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600">
                  Apply
                </button>
                <button
                  @click="toggleReviews(job.id)"
                  class="mt-2 px-4 py-2 bg-green-500 text-white rounded hover:bg-green-600 m-2"
                >
                  Reviews
                </button>
                <div v-if="selectedJobId === job.id" class="mt-4">
                  <h3 class="font-semibold">Company Reviews</h3>
                  <textarea
                    v-model="newReview"
                    class="w-full mt-2 p-2 border rounded-lg"
                    placeholder="Write a review..."
                  ></textarea>
                  <button
                    @click="addReview(job.id)"
                    class="mt-2 px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600"
                  >
                    Submit Review
                  </button>
                  <div v-if="job.reviews.length" class="mt-4">
                    <h4 class="font-bold">Previous Reviews:</h4>
                    <ul>
                      <li
                        v-for="(review, index) in job.reviews"
                        :key="index"
                        class="border-b py-2 flex justify-between"
                      >
                        {{ review }}
                        <button
                          @click="deleteReview(job.id, index)"
                          class="ml-4 text-red-500 hover:underline"
                        >
                          Delete
                        </button>
                      </li>
                    </ul>
                  </div>
                </div>
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
      isDarkMode: false,
      newReview: '',
      selectedJobId: null,
      jobs: [
        {
          id: 1,
          title: 'Frontend Developer',
          company: 'Tech Corp',
          location: 'New York',
          type: 'Full-time',
          description: 'Develop modern web applications.',
          reviews: []
        },
        {
          id: 2,
          title: 'Backend Developer',
          company: 'Data Systems',
          location: 'San Francisco',
          type: 'Part-time',
          description: 'Build and maintain server-side logic.',
          reviews: []
        },
        {
          id: 3,
          title: 'Fullstack Developer',
          company: 'Web Solutions',
          location: 'Remote',
          type: 'Contract',
          description: 'Work on both frontend and backend tasks.',
          reviews: []
        },
        {
          id: 4,
          title: 'DevOps Engineer',
          company: 'Cloud Services',
          location: 'Austin',
          type: 'Full-time',
          description: 'Maintain and improve cloud infrastructure.',
          reviews: []
        },
        {
          id: 5,
          title: 'UI/UX Designer',
          company: 'Creative Inc.',
          location: 'Los Angeles',
          type: 'Part-time',
          description: 'Design user-friendly interfaces and experiences.',
          reviews: []
        },
        {
          id: 6,
          title: 'Project Manager',
          company: 'Enterprise Solutions',
          location: 'Chicago',
          type: 'Contract',
          description: 'Lead and manage software development projects.',
          reviews: []
        },
        {
          id: 7,
          title: 'Data Analyst',
          company: 'Analytics Co.',
          location: 'Seattle',
          type: 'Full-time',
          description: 'Analyze and interpret complex data sets.',
          reviews: []
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
    },
    toggleDarkMode() {
      this.isDarkMode = !this.isDarkMode
    },
    setAutoDarkMode() {
      const currentHour = new Date().getHours()
      this.isDarkMode = currentHour >= 18 || currentHour < 6
    },
    toggleReviews(jobId) {
      this.selectedJobId = this.selectedJobId === jobId ? null : jobId
      this.newReview = '' // Reset review input
    },
    addReview(jobId) {
      if (this.newReview.trim()) {
        const job = this.jobs.find((j) => j.id === jobId)
        if (job) {
          job.reviews.push(this.newReview.trim())
          this.newReview = '' // Clear input after submission
        }
      }
    },
    deleteReview(jobId, reviewIndex) {
      const job = this.jobs.find((j) => j.id === jobId)
      if (job && reviewIndex > -1) {
        job.reviews.splice(reviewIndex, 1)
      }
    }
  },
  created() {
    this.setAutoDarkMode() // Set initial dark mode based on time
    setInterval(this.setAutoDarkMode, 60000) // Check every minute
  }
}
</script>
