#If your Vagrant version is lower than 1.5, you can still use this provisioning
#by commenting or removing the line below and providing the config.vm.box_url parameter,
#if it's not already defined in this Vagrantfile. Keep in mind that you won't be able
#to use the Vagrant Cloud and other newer Vagrant features.
Vagrant.require_version ">= 2.0"

VM_NAME = "Instant Localhost"
PORT_TO_FORWARD = "8080"

# Check to determine whether we're on a windows or linux/os-x host,
# later on we use this to launch ansible in the supported way
# source: https://stackoverflow.com/questions/2108727/which-in-ruby-checking-if-program-exists-in-path-from-ruby
def which(cmd)
    exts = ENV['PATHEXT'] ? ENV['PATHEXT'].split(';') : ['']
    ENV['PATH'].split(File::PATH_SEPARATOR).each do |path|
        exts.each { |ext|
            exe = File.join(path, "#{cmd}#{ext}")
            return exe if File.executable? exe
        }
    end
    return nil
end
Vagrant.configure("2") do |config|
    config.vm.provider :virtualbox do |v|
        v.name = VM_NAME
        v.customize [
            "modifyvm", :id,
            "--name", VM_NAME,
            "--memory", 512,
            "--natdnshostresolver1", "on",
            "--cpus", 1,
            "--usbehci", "off"
        ]
    end

    config.vm.box = "bento/ubuntu-18.04"

    config.vm.network "forwarded_port", guest: 80, host: PORT_TO_FORWARD

    config.ssh.forward_agent = true

    #############################################################
    # Ansible provisioning (you need to have ansible installed)
    #############################################################
    if which('ansible-playbook')
        config.vm.provision "ansible" do |ansible|
            ansible.playbook = "ansible/provision.yml"
            ansible.compatibility_mode = "2.0"
            ansible.extra_vars = {
                base_url: "http://localhost:" + PORT_TO_FORWARD,
            }
        end
    end


    config.vm.synced_folder "../", "/vagrant"
end
