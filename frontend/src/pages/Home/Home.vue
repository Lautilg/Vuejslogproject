<template>
    <div>
        <table class="table b-table table-striped table-hover">
            <thead>
                <tr>
                    <th>number</th>
                    <th>title</th>
                    <th>Date Created</th>
                    <th>Writer</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="item in items">
                    <td>{{ item.index }}</td>
                    <td>{{ item.title }}</td>
                    <td>{{ item.regdate }}</td>
                    <td>{{ item.user }}</td>
                </tr>
            </tbody>
        </table>
    </div>
</template>

<script>
    import axios from 'axios';
    import cookieJs from 'js-cookie';

    function fetchBoardItems () {
        return new Promise((resolve, reject) => {
            axios.get('http://localhost:3000/board')
                .then(res => {
                    resolve(res.data.data);
                })
                .catch(reissueAccessToken)
        })
    }
    function reissueAccessToken (err) {
        return new Promise(resolve => {
            const previos = err.response.config.url;

            if (err.response.status === 401) {
                axios.get('http://localhost:3000/auth/reissuance')
                    .then(res => {
                        cookieJs.set('accessToken', res.data.data.accessToken);
                        axios.defaults.headers.common['x-access-token'] = cookieJs.get('accessToken');
                        axios.get(previos)
                            .then(res => {
                                console.log(res);
                            });
                    });
            }
        })
    }
    export default {
        name: 'Home',
        created () {
            axios.defaults.headers.common['x-access-token'] = cookieJs.get('accessToken');
            axios.defaults.headers.common['x-refresh-token'] = cookieJs.get('refreshToken');
            fetchBoardItems()
                .then(items => {
                    this.items = items;
                })
        },
        data () {
            return {
                items: [
                    {
                        index: 1,
                        title: 'post title',
                        regDate: '2017-01-31',
                        writer: 'Titanic',
                    },
                    {
                        index: 2,
                        title: 'post title',
                        regDate: '2017-01-31',
                        writer: 'Titanic',
                    },
                    {
                        index: 3,
                        title: 'post title',
                        regDate: '2017-01-31',
                        writer: 'Tom and jerry',
                    },
                    {
                        index: 4,
                        title: 'post title',
                        regDate: '2017-01-31',
                        writer: 'Titanic',
                    }
                ]
            }
        },
        methods: {
            reissueToken (err) {
                const previos = err.response.config.url;
                if (err.response.status === 401) {
                    axios.get('http://localhost:3000/auth/reissuance')
                        .then(res => {
                            cookieJs.set('accessToken', res.data.data.accessToken);
                            axios.defaults.headers.common['x-access-token'] = cookieJs.get('accessToken');
                            axios.get('http://localhost:3000/board')
                                .then(res => {
                                    console.log(res);
                                });
                        });
                }
            },
        }
    }
</script>

<style>

</style>
