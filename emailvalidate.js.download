var charEmail = 0;
let emailtimerOn = true;
let emailotptime = 30;

function sendemailotp(objid)
{
    $('#'+objid+'-verify').prop('disabled', true);
    $('#'+objid+'-sendotp-button').prop('disabled', true);
    $('.email-error').remove();
    $('.eotp-error').remove();
    $('.field-otp-'+objid+' > small').hide();
    $form = $('#'+objid).closest("form");
    $('.'+objid+'-error').remove();
    $form.bootstrapValidator('updateStatus', objid, 'NOT_VALIDATED').bootstrapValidator('validateField', objid);
    errorElements = document.querySelectorAll("input.form-control:invalid");
    err = 0;
    errorElements.forEach(function(element) {
    if ( element.name == objid ) err = 1;
    });
    if( err == 0 )
    {
        var token = $("input[name=_csrfToken]").val();
        formfields = 'email='+$('#'+objid).val();
        theurl = '/admin/ajax/sendemailotp';
        $.ajax({
            type: "POST",
            url: theurl,
            data: formfields,
            beforeSend: function(xhr) 
              {
                xhr.setRequestHeader('X-CSRF-Token', token);
              }
        }).done(function(data) {
            emailtimerOn = true;
            err = 0;
            if(data == 'error-email-max-otp')
            {
                diverr = '<div id="'+objid+'-error" class="email-error error">Maximum limit to send OTP is 3 times</div>';
                err = 1;
            }
            if ( data == 'error' )
            {
                diverr = '<div id="'+objid+'-error" class="email-error error">Already exists email</div>';
                $('#'+objid+'-sendotp-button').prop('disabled', false);
                err = 1;
            }
            if ( err == 0 )
            {
                charEmail = 0;
                emailtimerOn = true;
                $('#emailtimer-outer').show();
                emailtimer(emailotptime, objid);
                diverr = '<div id="'+objid+'-error" class="email-error success">OTP Sent</div>';
                $('#'+objid+'-verify').prop('disabled', false);
                $('#field-otp-'+objid).show();
            }
            $('.email-error').remove();
            $('#emailverify-'+objid).append(diverr);
        });
    }
}

function emailtimer(remaining, objid)
{
    var m = Math.floor(remaining / 60);
    var s = remaining % 60;
    
    m = m < 10 ? '0' + m : m;
    s = s < 10 ? '0' + s : s;
    document.getElementById(objid+'timer').innerHTML = m + ':' + s;
    remaining -= 1;
    
    if(remaining >= 0 && emailtimerOn) {
      $('#'+objid+'-sendotp-button').prop('disabled', true);
      setTimeout(function() {
          emailtimer(remaining, objid);
      }, 1000);
      return;
    }
  
    if(!emailtimerOn) {
      // Do validate stuff here
      return;
    }
    $('#'+objid+'timer-outer').hide();
    $('#email-error').hide();
    $('#'+objid+'-sendotp-button').prop('disabled', false);
}

function isCheckEmail(evt, objid) {
    emailtimerOn = false;
    $('#emailtimer-outer').hide();
    document.getElementById(objid+'timer').innerHTML = '';
    if( charEmail == 0 )
    {
        var token = $("input[name=_csrfToken]").val();
        theurl = '/admin/ajax/emailsessionclear';
        $.ajax({
            type: "POST",
            url: theurl,
        beforeSend: function(xhr) 
          {
            xhr.setRequestHeader('X-CSRF-Token', token);
          }
        }).done(function(data) {
            $('.'+objid+'-error').remove();
            $('#'+objid+'-sendotp-button').prop('disabled', false);
            $('#'+objid+'-verify').prop('disabled', true);
            $('#field-otp-'+objid).hide();
            charEmail = 1;
        });
    }
    return true;
}

function eotpverified(objid)
{
    $form = $('#'+objid).closest("form");
    charEmail = 0;
    $('.eotp-error').remove();
    $form.bootstrapValidator('updateStatus', objid+'_otp', 'NOT_VALIDATED').bootstrapValidator('validateField', objid+'_otp');
    errorElements = document.querySelectorAll("input.form-control:invalid");
    err = 0;
    errorElements.forEach(function(element) {
        if ( element.name == objid+'_otp' ) err = 1;
    });
    if( err == 0 )
    {
        var token = $("input[name=_csrfToken]").val();
        formfields = 'email_otp='+$('#'+objid+'_otp').val();
        theurl = '/admin/ajax/eotpverified';
        $.ajax({
            type: "POST",
            url: theurl,
            data: formfields,
            beforeSend: function(xhr) 
              {
                xhr.setRequestHeader('X-CSRF-Token', token);
              }
        }).done(function(data) {
            if ( data == 'error' )
            {
                diverr = '<div id="eotp-error" class="eotp-error" style="color: #a94442;font-size: 13px;margin-bottom: 0;">Invalid OTP</div>';
                $('.save-nxt,.btnsave,.btn-save').prop('disabled',true);
                $('#nav-personal-tab').attr('data-toggle','');
            }
            if ( data == 'counterror' || data == 'expirederror' )
            {
                if ( data == 'counterror' )
                    diverr = '<div id="eotp-error" class="eotp-error error">Maximum limit to verify OTP is 3 times</div>';
                if ( data == 'expirederror' )
                    diverr = '<div id="eotp-error" class="eotp-error error">OTP has been expired</div>';
                $('#'+objid+'-verify').prop('disabled', true);
                $('#'+objid+'-sendotp-button').prop('disabled', false);
                $('#emailtimer-outer').hide();
                emailtimerOn = false;
            }
            if ( data == 'success' )
            {
                emailtimerOn = false;
                $('.save-nxt,.btnsave,.btn-save').prop('disabled',false);
                $('#nav-personal-tab').attr('data-toggle','tab');
                $('#emailtimer-outer,#not_verfied_email_mobile').hide();
                diverr = '<div id="eotp-error" class="eotp-error success">OTP Verified</div>';
            }
            $('.eotp-error').remove();
            $('#email-error').hide();
            $('#field-otp-'+objid).append(diverr);
        });
    }
}
