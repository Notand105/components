<script>
    import {primary} from '../utils/colorstore'
    import {accent} from '../utils/colorstore'
</script>

<div style="--primary-color: {$primary}; --accent-color:{$accent} ">
    <div class="show" >
        Patos
    </div>
</div>

<style>
    div{
        color:white;
        padding: 1rem;
    }
    .show{
        background-color: var(--primary-color);
        transition: .3s ease;
    }
    .show:hover{
        background-color: var(--accent-color);
        transition: .3s ease;
        cursor: pointer;
    }
    
</style>
    