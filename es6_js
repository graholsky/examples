    $(document).ready(function () {
        $('.steps-wrapp').each(function (index, el) {
            new Instruction(el);
        });
    });

    class Instruction {
        constructor(el) {
            this.el = el;
            var first_step = $(el).attr('data-last-step');
            this.active = first_step;
            this.counter = $(el).find('.steps-counter-box ol');
            Instruction.colection.push(this);
            this.createSteps();
            this.setActive();
            this.save_step_for_user();
        }

        setEventCounterPanel(active) {
            var self = this;
            if (active > 0) {
                this.counter.find('li').css({'cursor': 'default'});
                this.counter.find('li:nth-child(' + (active) + ')').css({'cursor': 'pointer'});
                var self = this;
                this.counter.find('li:nth-child(' + (active) + ')').click(function () {
                    self.active--;
                    self.setActive();
                    self.save_step_for_user();
                });

            }
            $(this.el).parent().find('.user-tools-list .done').click(function (e) {
                e.preventDefault();
                self.set_last_step();
            });

        }

        createSteps() {
            this.steps = new Array();
            var self = this;
            var counter_steps = 0;
            var all_steps_count = $(this.el).find('>.step-box-main-wrapper').length;
            $(this.el).find('>.step-box-main-wrapper').each(function (index, el) {
                var type = $(el).attr('data-variations-type');
                if (counter_steps == 0) {
                    $(el).attr('data-first', true);
                }
                if (counter_steps == all_steps_count - 1) {
                    $(el).attr('data-last', true);
                }
                self.steps.push(new Instruction.types[type](el, self));
                counter_steps++;
            });
        }

        setActive() {
            for (var i = 0; i < this.steps.length; i++) {
                if (this.active == i) {
                    this.steps[i].show();

                }
                else {
                    this.steps[i].hide();
                }
            }
            this.counter.find('li').removeClass('active').slice(0, (parseInt(this.active) + 1)).addClass('active');
            this.counter.find('li').unbind("click");
            this.counter.find('li').css({'cursor': 'default'});
            this.setEventCounterPanel(this.active);

        }


        next() {
            this.active++;
            this.setActive()
            this.save_step_for_user();
        }

        prev() {
            this.active--;
            this.setActive();
            this.save_step_for_user();
        }

        set_last_step() {
            var all_steps_count = $(this.el).find('>.step-box-main-wrapper').length;
            this.active = all_steps_count - 1;
            this.setActive();
            this.save_step_for_user();
        }

        set_first_step() {
            this.active = 0;
            this.setActive();
            this.save_step_for_user();
        }

        save_step_for_user() {
            var data = {
                'action': 'current_tip',
                'tip': $(this.el).data('tip'),
                'step': parseInt(this.active) + 1,
                'completed': (this.active == this.steps.length - 1) ? 1 : 0
            };

            $.ajax({
                url: '/wp/wp-admin/admin-ajax.php',
                data: data,
                type: 'POST',
                success: function (res) {
                    // console.log(res);
                }
            })

        }
    }


    Instruction.colection = new Array();
    Instruction.types = {};

    class InstructionStep {
        constructor(el, instruction) {
            this.el = el;
            this.instruction = instruction;
            this.nextBtn = $(this.el).find('.btn-to-next-step');
            this.prevBtn = $(this.el).find('.btn-to-previous-step');

            if ($(this.el).data('last')) {
                this.nextBtn = $(this.el).find('.btn-start-new-tip');
                this.prevBtn = $(this.el).find('.btn-restart-tip');
                $(this.el).find('.btn-to-previous-step').addClass('btn-invisible');
                $(this.el).find('.btn-to-next-step').remove();
            }
            if ($(this.el).data('first')) {
                $(this.el).find('.btn-to-previous-step').remove();
                this.prevBtn = $();

            }

            this.addEvents();
            this.validate();
        }

        addEvents() {
            var self = this;
            $(this.el).find('.btn-to-previous-step').click(function (e) {
                e.preventDefault();
                self.instruction.prev();
            });

            $(this.el).find('.btn-to-next-step').click(function (e) {
                e.preventDefault();
                self.instruction.next();
            });

            $(this.el).find('.btn-restart-tip').click(function (e) {
                e.preventDefault();
                self.instruction.set_first_step();
            });


        }

        validate() {
            this.prevBtn.removeClass('btn-invisible');
            this.nextBtn.removeClass('btn-invisible');
        }

        hide() {
            $(this.el).removeClass('step-box-active');
            $(this.el).parent().find('.steps-categories-review').addClass('message-invisible');
            $(this.el).parent().find('.categories-area-steps').addClass('message-invisible');
        }

        show() {
            $(this.el).addClass('step-box-active');
            if ($(this.el).data('last')) {
                $(this.el).parent().find('.steps-categories-review').removeClass('message-invisible');
                $(this.el).parent().find('.categories-area-steps').removeClass('message-invisible');
            }
        }

    }

    class answers extends InstructionStep {
        addEvents() {
            var self = this;
            $(this.el).find('.answers-rado').change(function () {
                var true_answer = $(this).attr('data-true');
                self.show_message();
                self.validate(true_answer);
            });
            super.addEvents();
        }

        validate(true_answer) {
            if (true_answer == 'check') {
                this.nextBtn.removeClass('btn-disabled');
                $(this.el).find('.message-holder-true').removeClass('message-invisible');
                $(this.el).find('.message-holder-false').addClass('message-invisible');
            }
            else {
                $(this.el).find('.message-holder-false').removeClass('message-invisible');
                $(this.el).find('.message-holder-true').addClass('message-invisible');
                this.nextBtn.addClass('btn-disabled');
            }
            super.validate();

        }

        show_message() {
            $(this.el).find('.answers-description').removeClass('message-invisible');
        }
    }

    Instruction.types.answers = answers;

    class buttons extends InstructionStep {
        addEvents() {
            var self = this;
            $(this.el).find('.btn-answer').click(function (e) {
                var true_answer = $(self.el).find('.btn-buttons').attr('data-true');
                e.preventDefault();
                if ($(this).hasClass('btn-left')) {
                    var click_button = 'left';
                }
                if ($(this).hasClass('btn-right')) {
                    var click_button = 'right';
                }

                self.validate(true_answer == click_button);
                $(self.el).find('.btn-answer').addClass('btn-invisible');
                $(self.el).find('.btn-to-previous-step').addClass('btn-invisible');
                $(self.el).find('.description-answer-button').removeClass('message-invisible');
                self.nextBtn.removeClass('btn-invisible');
                self.prevBtn.removeClass('btn-invisible');
            });
            super.addEvents();
        }


        validate(result) {
            super.validate();
            this.nextBtn.addClass('btn-invisible');
            this.prevBtn.addClass('btn-invisible');
            $(this.el).find('.btn-to-previous-step').removeClass('btn-invisible');
            $(this.el).find('.buttons-description').removeClass('message-invisible');
            if (result) {
                $(this.el).find('.message-holder-true').removeClass('message-invisible');
            }
            else if (typeof result != 'undefined') {
                $(this.el).find('.message-holder-false').removeClass('message-invisible');
            }
        }
    }

    Instruction.types.buttons = buttons;

    class password extends InstructionStep {
        addEvents() {
            var self = this;
            $(this.el).find('.password-form-field').on('keyup', function () {
                var pass_value = $(this).val();
                var result_veryfication = verification_password(pass_value);
                self.validate(result_veryfication);
            });
            super.addEvents();
        }

        validate(result_veryfication) {
            super.validate();
            $(this.el).find('.form-info-box-holder').removeClass('message-invisible');
            var very_weak = $(this.el).find('.very-weak');
            var strong = $(this.el).find('.strong');
            var medium = $(this.el).find('.medium');
            var invalid = $(this.el).find('.invalid-symbols');
            if (result_veryfication == 'very_weak') {
                very_weak.removeClass('message-invisible');
                medium.addClass('message-invisible');
                strong.addClass('message-invisible');
                invalid.addClass('message-invisible');
                this.nextBtn.addClass('btn-disabled');

            }
            if (result_veryfication == 'medium') {
                very_weak.addClass('message-invisible');
                medium.removeClass('message-invisible');
                strong.addClass('message-invisible');
                invalid.addClass('message-invisible');
                this.nextBtn.addClass('btn-disabled');
            }
            if (result_veryfication == 'invalid') {
                invalid.removeClass('message-invisible');
                very_weak.addClass('message-invisible');
                medium.addClass('message-invisible');
                strong.addClass('message-invisible');
                this.nextBtn.addClass('btn-disabled');
            }
            if (result_veryfication == 'strong') {
                very_weak.addClass('message-invisible');
                medium.addClass('message-invisible');
                strong.removeClass('message-invisible');
                this.nextBtn.removeClass('btn-disabled');
            }

        }
    }

    Instruction.types.password = password;

    class icon_text extends InstructionStep {

    }

    Instruction.types.icon_text = icon_text;

    class image extends InstructionStep {

    }

    Instruction.types.image = image;

    class image_text extends InstructionStep {
        addEvents() {
            $(this.el).find('.step-sub-box:first').addClass('step-sub-box-active');
            $(this.el).find('.btn-next-sub-step').click(function (e) {
                e.preventDefault();
                $(this).parent().parent().parent().removeClass('step-sub-box-active');
                $(this).parent().parent().parent().next().addClass('step-sub-box-active');
            });
            $(this.el).find('.btn-prev-sub-step').click(function (e) {
                e.preventDefault();
                $(this).parent().parent().parent().removeClass('step-sub-box-active');
                $(this).parent().parent().parent().prev().addClass('step-sub-box-active');
            });
            super.addEvents();
        }
    }

    Instruction.types.image_text = image_text;


    class video extends InstructionStep {

    }

    Instruction.types.video = video;
