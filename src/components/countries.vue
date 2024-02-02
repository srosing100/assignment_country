
<script>
import axios from 'axios';
import 'bootstrap/dist/css/bootstrap.css';

//  import axios from 'axios';

export default {
    data() {
        return {
            countries: [],
            searchQuery: '',
            sortOrder: 'asc',
            currentPage: 1,
            itemsPerPage: 25,
            selectedCountry: null
        };
    },
    computed: {
        filteredCountries() {
            let filtered = this.countries;

            if (this.searchQuery) {
                filtered = filtered.filter(country => {
                    const regex = new RegExp(this.searchQuery, 'i');
                    return regex.test(country.name.official);
                });
            }

            if (this.sortOrder === 'asc') {
                filtered.sort((a, b) => a.name.official.localeCompare(b.name.official));
            } else if (this.sortOrder === 'desc') {
                filtered.sort((a, b) => b.name.official.localeCompare(a.name.official));
            }

            const start = (this.currentPage - 1) * this.itemsPerPage;
            const end = start + this.itemsPerPage;
            return filtered.slice(start, end);
        },
    },
    mounted() {
        this.fetchCountries();
        this.totalPages();
        // console.log(this.countries.length);
    },
    methods: {
        fetchCountries() {
            axios.get('https://restcountries.com/v3.1/all')
                .then(response => {
                    this.countries = response.data;

        // console.log(this.countries.length);

                })
                .catch(error => {
                    console.error(error);
                });
        },
        totalPages() {
            // return Math.ceil(this.filteredCountries.length / this.itemsPerPage);
            return Math.ceil(this.countries.length / this.itemsPerPage);
            console.log(this.countries.length)
        },
        showModal(country) {
            this.selectedCountry = country;
            document.body.classList.add('modal-open');
        },
        closeModal() {
            this.selectedCountry = null;
            document.body.classList.remove('modal-open');
        },
        prevPage() {
            if (this.currentPage > 1) {
                this.currentPage--;
            }
        },
        nextPage() {
            if (this.currentPage <  10 ) {
                this.currentPage++;
            }
        }
    }
};
</script>

<template>
    <div class="container">
        <div class="row">
            <div class="col-xl-12">
                <h1>Countries List</h1>
            </div>
        </div>

        <div class="row mt-4">
            <div class="col-xl-8">
                <input type="text" v-model="searchQuery" placeholder="Search by Country Name" class="form-control" />
            </div>
            <div class="col-xl-4">
                <select v-model="sortOrder" class="form-select">
                    <option value="asc">Ascending</option>
                    <option value="desc">Descending</option>
                </select>
            </div>
        </div>


        <table class="table table-condensed mt-4">
            <thead class="table-dark">
                <tr>
                    <th>Flag</th>
                    <th>Country Name</th>
                    <th>2 Character Code</th>
                    <th>3 Character Code</th>
                    <th>Native Country Name</th>
                    <th>Alternative Country Name</th>
                    <th>Country Calling Codes</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="country in filteredCountries" :key="country.cca3">
                    <td width="80" height="90"><img :src="country.flags.png" alt="Flag" width="100%" height="100%" /></td>
                    <td width="120" class="show" @click="showModal(country)">{{ country.name.official }}</td>
                    <td width="150">{{ country.cca2 }}</td>
                    <td width="150">{{ country.cca3 }}</td>
                    <td width="150">{{ country.name.nativeName }}</td>
                    <td width="150">{{ country.altSpellings }}</td>
                    <td width="150">{{ country.idd }}</td>
                </tr>
            </tbody>
        </table>

        <div v-if="selectedCountry">
        
            <h2>{{ selectedCountry.name.official }}</h2>
            <p>Capital: {{ selectedCountry.capital }}</p>
            <p>Population: {{ selectedCountry.population }}</p>
            <p>Region: {{ selectedCountry.region }}</p>
            <p>Subregion: {{ selectedCountry.subregion }}</p>
            <!-- Add more information about the selected country as needed -->

            <!-- Modal component -->
            <div class="modal" @click="closeModal">
                <div class="modal-content" @click.stop>
                    <h2>{{ selectedCountry.name.official }}</h2>
                    <p>Capital: {{ selectedCountry.capital }}</p>
                    <p>Population: {{ selectedCountry.population }}</p>
                    <p>Region: {{ selectedCountry.region }}</p>
                    <p>Subregion: {{ selectedCountry.subregion }}</p>
                    <!-- Add more information about the selected country as needed -->
                </div>
            </div>
        </div>

        <div>
            <button class="btn btn-primary" :disabled="currentPage === 1" @click="prevPage">Previous</button>
            <span class="btn">{{ currentPage }}</span>
            <button class="btn btn-primary" :disabled="currentPage === totalPages" @click="nextPage">Next</button>
        </div>
    </div>
</template>


<style>
table thead tr th {
    width: 100px;
    font-size: 14px;
}

.modal {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%0%;
    height: 100%;
    background-color: rgba(0, 0, 0, 0.5);
    display: flex;
    justify-content: center;
    align-items: center;
}

.modal-content {
    background-color: #fff;
    padding: 20px;
    width: 30%;
}
.show{
    cursor: pointer;
}
</style>