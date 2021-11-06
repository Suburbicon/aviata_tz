<template>
    <div class="filter">
        <div class="filter__wrap-header">
            <h4 class="filter__header">{{ title }}</h4>
            <IconCloseNew @click="clearSearch" v-show="showClearIconToggle" class="filter__svg" />
        </div>
        <div class="filter__content">
            <template v-for="item in content.content" :key="item.id">
                <label class="filter__row">
                    <label :for="item.key" class="filter__label">
                        <input
                            :id="item.key"
                            type="checkbox"
                            class="filter__checkbox"
                            :value="item.key"
                            v-model="computedValue"
                        />
                        <span class="filter__custom-checkbox"></span>
                        <span class="filter__checkbox-text">{{ item.name }}</span>
                    </label>
                </label>
            </template>
        </div>
    </div>
</template>

<script>
import { ref, watch, computed } from 'vue';
import IconCloseNew from '../components/Icons/IconCloseNew.vue';

export default {
    name: 'AppFilter',
    components: {
        IconCloseNew
    },

    props: {
        title: {
            type: String,
            default: ''
        },
        content: {
            type: Object,
            default: {}
        },
        showClearIconToggle: {
            type: Boolean,
            default: false
        },
        valueCheckbox: {
            type: Array,
            default: []
        }
    },

    emits: ['update:valueCheckbox', 'clear'],

    setup(props, { emit }) {
        const computedValue = computed({
            get() {
                return props.valueCheckbox;
            },

            set(value) {
                emit('update:valueCheckbox', value);
            }
        });

        const clearSearch = () => {
            emit('clear');
        };

        return {
            clearSearch,
            computedValue
        };
    }
};
</script>

<style lang="scss" scope>
.filter {
    width: 100%;
    padding: 12px 0;
    background-color: #f5f5f5;
    border-radius: 4px;

    &__wrap-header {
        padding: 0 15px;
        display: flex;
        align-items: center;
        justify-content: space-between;
        margin-bottom: 12px;
    }

    &__header {
        font-size: 14px;
        font-weight: 700;
        line-height: 20px;
    }

    &__svg {
        cursor: pointer;
        fill: #b9b9b9;

        &:hover {
            fill: #7284e4;
        }
    }

    &__row {
        display: block;
        height: 30px;
        padding-left: 15px;
        padding-top: 3px;
        cursor: pointer;
        user-select: none;

        &:hover {
            background-color: #ebebeb;
        }
        &:hover .filter__custom-checkbox {
            background-image: url(../assets/checkbox_hover.svg);
        }
    }

    &__label {
        font-size: 12px;
        font-weight: 400;
        line-height: 16px;
        cursor: pointer;
        padding-left: 15px;
    }

    &__checkbox {
        position: absolute;
        -webkit-appearance: none;
        -moz-appearance: none;
        appearance: none;
    }

    &__custom-checkbox {
        position: absolute;
        width: 1em;
        height: 1em;
        background-image: url(../assets/Rectangle.svg);
        border-radius: 2px;
        margin: 5px 0 0 -15px;

        &:hover {
            background-image: url(../assets/checkbox_hover.svg);
        }
    }

    &__checkbox:checked + &__custom-checkbox {
        background-image: url(../assets/checkbox_active.svg);
    }

    &__checkbox-text {
        margin-left: 10px;
    }
}
</style>
